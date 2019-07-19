<template>
  <div id="app">
    <trans msg="test"/>
  </div>
</template>

<script>
import trans from './components/trans.vue'

export default {
  name: 'app',
  components: {
    trans,
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
