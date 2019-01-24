<template>
  <router-view/>
</template>

<script>

  export default {

  }
</script>

<style lang="less">
  @import "./assets/less/def.less";
  @import "https://at.alicdn.com/t/font_975308_uh6yotsgj6.css";

  .vue-lazyload {
    opacity: 0;
    transition: opacity .4s ease;
  }

  .vue-lazyload[lazy=loaded] {
    opacity: 1;
  }

  .vue-lazyload[lazy=loading] {
    opacity: 1;
  }

  .vue-lazyload[lazy=error] {
    opacity: 1;
  }
</style>
