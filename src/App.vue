<template>
  <img :src="require('./assets/stack.png')" alt="stack" class="stack" />
  <Mp4ToGif />
</template>

<script>
import Mp4ToGif from "./components/Mp4ToGif.vue";
export default {
  name: "App",
  components: {
    Mp4ToGif,
  },
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
  background: #dbdeff;
}

.stack {
  position: fixed;
  right: 15px;
  top: 15px;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
</style>
