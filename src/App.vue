<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
  export default {
    name: 'App'
  }
</script>

<style lang="less">
  @import "../node_modules/vue2-animate/src/less/vue2-animate.less";
  @import './variable';
  @animationDuration: 2s;
  ul{
    list-style: none;
    margin: 0;
    padding: 0;
  }
  html, body, #app {
    width: @max;
    height: @max;
    margin: 0;
    overflow: hidden;
  }
</style>
