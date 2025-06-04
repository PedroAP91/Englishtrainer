<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <EnglishQuiz />
</template>

<script>
import EnglishQuiz from './components/EnglishQuiz.vue'

export default {
  name: 'App',
  components: {
    EnglishQuiz
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
