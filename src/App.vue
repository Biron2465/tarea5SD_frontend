<template>
  <div id="app">
    <div v-if="isAuthenticated">
      <nav>
        <router-link to="/authors">Authors</router-link>
        <router-link to="/publishers">Publishers</router-link>
        <router-link to="/books">Books</router-link>
        <button class="btn-logout" @click="logout">Logout</button>
      </nav>
      <router-view />
    </div>
    <div v-else class="auth-modal">
      <h2>Enter Password</h2>
      <input type="password" v-model="password" placeholder="Enter password">
      <button @click="authenticate">Submit</button>
      <p v-if="authError" class="error">Incorrect password. Please try again.</p>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      isAuthenticated: false,
      password: '',
      authError: false
    };
  },
  methods: {
    authenticate() {
      if (this.password === 'tarea5') { // Cambia esto por la contraseña correcta
        localStorage.setItem('authPassword', this.password);
        this.isAuthenticated = true;
        this.authError = false;
      } else {
        this.authError = true;
      }
    },
    logout() {
      localStorage.removeItem('authPassword'); // Elimina la clave de autenticación
      this.isAuthenticated = false; // Oculta el contenido y muestra el formulario de autenticación
      this.password = ''; // Limpia el campo de la contraseña
    }
  },
  mounted() {
    const savedPassword = localStorage.getItem('authPassword');
    if (savedPassword === 'tarea5') { // Cambia esto por la contraseña correcta
      this.isAuthenticated = true;
    }
  }
};
</script>

<style>
#app {
  font-family: 'Arial', sans-serif;
  background-color: #1e1e1e;
  color: #ffffff;
  min-height: 100vh;
  padding: 20px;
}

nav {
  background-color: #333;
  padding: 10px;
  border-radius: 8px;
  display: flex;
  gap: 15px;
}

nav a {
  text-decoration: none;
  color: #42b983;
  font-size: 18px;
  transition: color 0.3s ease;
}

nav a:hover {
  color: #f39c12;
}

nav a.router-link-exact-active {
  font-weight: bold;
  color: #e74c3c;
}

.btn-logout {
  background-color: #e74c3c;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 18px;
  transition: background-color 0.3s ease;
  margin-left: auto; /* Alinea el botón a la derecha */
}

.btn-logout:hover {
  background-color: #c0392b;
}

.auth-modal {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #222;
  padding: 20px;
  border-radius: 10px;
  margin: 20px auto;
  max-width: 300px;
}

.auth-modal h2 {
  color: #f39c12;
}

.auth-modal input {
  padding: 10px;
  margin: 10px 0;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.auth-modal button {
  padding: 10px 20px;
  background-color: #2ecc71;
  border: none;
  color: white;
  border-radius: 5px;
}

.error {
  color: #e74c3c;
}
</style>
