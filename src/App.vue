<template>
  <div id="app">
    <header>
      <img alt="KAVI Logo" src="./assets/KAVI_logo_black.png"/>
      <h1>Shoutbox</h1>
    </header>

    <div class="container">
      <ShoutForm />
    </div>
  </div>
</template>

<script>
import ShoutForm from './components/molecules/ShoutForm.vue';
export default {
  name: 'app',
  components: {
    ShoutForm
  }
}
</script>

<style>
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
