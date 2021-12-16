<template>
  <div id="app">
    <ComponentWithMap />
    <Sncf msg="Welcome to Your Vue.js App"/>
  </div>

</template>

<script>
import Sncf from './components/Sncf.vue'
import ComponentWithMap from './components/ComponentWithMap.vue'


export default {
  name: 'App',
  components: {
    Sncf,
    ComponentWithMap
  }
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
