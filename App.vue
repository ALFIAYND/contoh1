<template>
  <div>
    <header>
      <nav>
        <ul>
          <li><a href="#" @click="showPost">Post</a></li>
          <li><a href="#" @click="showTodos">Todos</a></li>
        </ul>
      </nav>
    </header>

    <!-- Konten untuk Post -->
    <div v-if="selectedTab === 'post'">
      <h1>Postingan Pengguna</h1>
      <!-- Select option untuk memilih pengguna -->
      <select v-model="selectedUser" @change="fetchUserPosts">
        <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
      </select>

      <!-- Tampilkan postingan pengguna yang dipilih -->
      <div v-for="post in userPosts" :key="post.id">
        <h2>{{ post.title }}</h2>
        <p>{{ post.body }}</p>
      </div>
    </div>

    <!-- Konten untuk Todos -->
    <div v-if="selectedTab === 'todos'">
      <h1>Daftar Todos</h1>
      <!-- Menampilkan daftar todos dalam bentuk checkbox jika showCheckbox true -->
      <ul>
        <!-- Melakukan loop pada daftar todos -->
        <li v-for="todo in todos" :key="todo.id">
          <!-- Checkbox untuk menandai status penyelesaian todo -->
          <input v-if="showCheckbox" type="checkbox" :id="todo.id" v-model="todo.completed" @change="updateTodo(todo)">
          <!-- Label untuk menampilkan teks todo -->
          <label :for="todo.id">{{ todo.title }}</label>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      selectedTab: 'post', // Tab yang aktif secara default
      users: [], // Data pengguna
      selectedUser: null, // Pengguna yang dipilih dari dropdown
      userPosts: [], // Postingan pengguna yang dipilih
      todos: [], // Daftar todos
      showCheckbox: false // Menentukan apakah checkbox harus ditampilkan atau tidak
    };
  },
  created() {
    this.fetchUsers(); // Ambil data pengguna saat komponen dibuat
  },
  methods: {
    async fetchUsers() {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/users');
        const data = await response.json();
        this.users = data;
      } catch (error) {
        console.error('Gagal mengambil data pengguna:', error);
      }
    },
    async fetchUserPosts() {
      if (!this.selectedUser) return;

      try {
        const response = await fetch(`https://jsonplaceholder.typicode.com/posts?userId=${this.selectedUser}`);
        const data = await response.json();
        this.userPosts = data;
      } catch (error) {
        console.error('Gagal mengambil postingan pengguna:', error);
      }
    },
    async fetchTodos() {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/todos');
        const data = await response.json();
        // Atur nilai awal completed menjadi false untuk setiap todo
        this.todos = data.map(todo => ({
          ...todo,
          completed: false
        }));
        // Set showCheckbox menjadi true setelah todos diambil
        this.showCheckbox = true;
      } catch (error) {
        console.error('Gagal mengambil daftar todos:', error);
      }
    },
    async updateTodo(todo) {
      try {
        if (todo.completed) {
          // Menampilkan pesan di log jika todo dicentang
          console.log(`Todo dengan ID ${todo.id} dicentang:`, todo);
        } else {
          // Menghapus pesan dari log jika todo dicentang
          console.clear();
        }
      } catch (error) {
        console.error('Gagal memperbarui todo:', error);
      }
    },
    showPost() {
      this.selectedTab = 'post'; // Menampilkan konten Post
    },
    showTodos() {
      this.selectedTab = 'todos'; // Menampilkan konten Todos
      this.fetchTodos(); // Ambil daftar todos saat tab Todos dipilih
    }
  }
}
</script>

<style>
/* Gaya CSS untuk header Anda akan diletakkan di sini */
header {
  background-color: #333;
  color: #fff;
  padding: 15px 0;
}

nav ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

nav ul li {
  display: inline;
  margin-right: 20px;
}

nav ul li a {
  color: #fff;
  text-decoration: none;
}
</style>
