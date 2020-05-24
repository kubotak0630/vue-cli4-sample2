<template>
  <div id="app">
    <HelloWorld msg="Welcome to Your Vue.js App1" />
    <el-button type="primary">Primary</el-button>
    <FileDropBox text_label="FileDrop"></FileDropBox>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import FileDropBox from "./components/FileDropBox.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
    FileDropBox
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
</style>
