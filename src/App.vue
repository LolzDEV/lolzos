<template>
  <div id="app">
    <Terminal />
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import Terminal from "./components/Terminal.vue"

export default Vue.extend({
  name: "App",
  components: {
    Terminal
  },
});
</script>

<style lang="scss">
  body {
    background-color: #212121;
  }
</style>
