<template>
  <div id="app">
    <SizeRecommender />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import SizeRecommender from './components/SizeRecommender.vue';

export default defineComponent({
  name: 'App',
  components: {
    SizeRecommender,
  },
});
</script>

<style>
body {
  margin: 0;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: #2c3e50;
}
</style>