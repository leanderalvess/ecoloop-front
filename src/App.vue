<template>
  <div id="app">
    <nav class="navbar">
      <router-link to="/" class="logo">
        <h2 class="navbar-brand">Ecoloop's</h2>
      </router-link>
      <div class="nav-links">
        <router-link to="/checkout" class="btn btn-cart">🛒 Cart</router-link>
      </div>
    </nav>
    <router-view />
  </div>
</template>

<script lang="ts">
export default {
  name: 'App',
};
</script>

<style lang="scss">
body {
  margin: 0;
  font-family: 'Poppins', sans-serif;
  background-color: #F0F0F0;
  color: #222222;
}

.logo {
  text-decoration: none;
}

.navbar {
  background-color: #333333;
  border-radius: 25px;
  padding: 10px;
  text-align: center;
  display: flex;
  align-items: center;
  justify-content: center;

  .navbar-brand {
    font-size: 2rem;
    font-weight: bold;
    color: #F0F0F0;
    margin-left: 30px;
    margin-top: 5px;
  }

  .nav-links {
    margin-right: 20px;
    display: flex;
    gap: 15px;

    .btn-link {
      color: #007bff;
      font-size: 1rem;
      text-decoration: none;
      padding: 10px 15px;
      border-radius: 5px;

      &:hover {
        background-color: #f0f0f0;
      }
    }

    .btn-cart {
      color: #F0F0F0;
      padding: 10px 20px;
      border-radius: 5px;
      font-weight: bold;
      border: none;
      text-decoration: none;

      &:hover {
        background-color: #555555;
      }
    }
  }
}

.container {
  padding: 20px;
  margin: auto;
  max-width: 1200px;
}

h1 {
  font-size: 2.5rem;
  text-align: center;
  margin-bottom: 2rem;
  color: #333333;
}

@media (max-width: 768px) {
  .navbar {
    flex-direction: column;
    gap: 10px;
  }
}
</style>
