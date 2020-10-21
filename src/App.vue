<template>
  <div id="app">
    <todo />
    <test></test>
  </div>
</template>

<script>
import todo from './components/todo.vue';
import test from './components/test.vue';

export default {
  components: {
    todo,
    test,
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
