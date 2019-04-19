<template>
  <div id="app">
    <WheelPopup/>
    <Popup />
  </div>
</template>

<script>
import WheelPopup from './components/WheelPopup.vue'
import Popup from './components/Popup'

export default {
  name: 'app',
  components: {
    WheelPopup,
    Popup,
  }
}
</script>

<style>
body {
  margin: 0;
  padding: 0;
}

#app {
  display: flex;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
