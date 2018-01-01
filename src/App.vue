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
html {
  overflow: hidden;
}

#app {
  background-image: url("./assets/mountains.jpg");
  margin: -8px;
}
</style>
