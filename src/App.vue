<template>
  <div id="app">
    <nav>
      <router-link to="/authors">Authors</router-link>
      <router-link to="/publishers">Publishers</router-link>
      <router-link to="/books">Books</router-link>
    </nav>
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'App'
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
}

nav a {
  margin: 0 15px;
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
</style>
F