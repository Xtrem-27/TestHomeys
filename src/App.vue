<template>
  <base-header />
  <date />
</template>

<script>
import BaseHeader from './components/BaseHeader.vue'
import Date from './components/Date.vue'

export default {
  name: 'App',
  components: {
    BaseHeader,
    Date
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
