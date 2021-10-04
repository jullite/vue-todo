<template>
  <div id="app">
    <todo/>
  </div>
</template>

<script>
import Todo from './components/Todo.vue'

export default {
  name: 'App',
  components: {
    Todo
  },
  data(){
    return {
      msg: "hello vue"
    }
  }
}
</script>

<style>
#app{
  margin-top: 40px;
}
</style>