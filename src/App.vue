<template>
  <div id="app">
    <router-view />
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style>
#app {
  background-image: url("./assets/mountains.jpg");
  margin: -8px;
  overflow: hidden;
}
</style>
