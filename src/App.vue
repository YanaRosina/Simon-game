<template>
  <div id="app">
  
      <SimonGame/>
  </div>
</template>

<script>
import SimonGame from './components/SimonGame.vue';
export default {
  components: {
    SimonGame
  },
}
</script>

<style lang="sass">

#app
  font-family: Avenir, Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  text-align: center
  color: #2c3e50
  display: flex
  justify-content: center
  align-items: center
  height: 100vh


  </style>