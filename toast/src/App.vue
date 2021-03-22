<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <!-- <HelloWorld msg="Welcome to Your Vue.js App" /> -->
  <Test msg="欢迎来到封装组件库，测试页面" />
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import Test from './components/Test.vue'

export default {
  name: 'App',
  components: {
    // HelloWorld,
    Test
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
