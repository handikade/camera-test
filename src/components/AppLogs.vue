<template>
  <div
    v-if="logs.length > 0"
    class="app-logs"
  >
    <p
      v-for="(log, key) in logs"
      :key="key"
    >{{ log }}</p>
  </div>
</template>

<script>
  export default {
    name: "AppLogs",
    props: {
      logs: {
        type: Array,
        default: []
      }
    }
  };
</script>

<style lang="scss" scoped>
  .app-logs {
    font-family: monospace;
    background-color: #f2f2f2;
    padding: 5px;
    color: red;
    font-size: 12px;

    p {
      margin-bottom: 10px;
    }
  }
</style>