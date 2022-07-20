<template>
  <div id="app">
    <HelloWorld :items="words"  />
  </div>
</template>

<script setup>
import HelloWorld from './components/HelloWorld.vue'
import {words} from './words';
</script>

<style>
*{
  margin: 0;
  padding: 0;
}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  color: #2c3e50;
}
</style>
