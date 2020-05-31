<template>
  <odometer id="app" />
</template>

<script>
import odometerDigit from './components/odometerDigit.vue'
import odometer from './components/odometer.vue'

export default {
  name: 'App',
  components: {
    odometer
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
