<template>
  <div class="container mt-4">
    <h1 class="text-center">Weather App</h1>
    <WeatherList />
  </div>
</template>

<script>
import WeatherList from './components/WeatherList.vue';
export default {
  components: { WeatherList }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
