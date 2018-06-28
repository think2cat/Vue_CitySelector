<template>
  <div id="app">
    <img src="./assets/logo.png">
    <CitySelect />
  </div>
</template>

<script>
import CitySelect from './components/CitySelect'

export default {
  name: 'app',
  components: {
    CitySelect
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
