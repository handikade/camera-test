<template>
  <div class="container">
    <slot/>
  </div>
</template>

<script>
  export default {
    name: "AppContainer"
  };
</script>

<style lang="scss" scoped>
  .container {
    max-width: 480px;
    background-color: #ffffff;
    padding: 30px 10px;
    margin: 0 auto;
    min-height: 100vh;
  }
</style>