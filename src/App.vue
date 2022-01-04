<template>
  <h1>Entre deux trains</h1>
  <div id="app">
    <Sncf />
  </div>

</template>

<script>
import Sncf from './components/Sncf.vue'


export default {
  name: 'App',
  components: {
    Sncf
  },
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
