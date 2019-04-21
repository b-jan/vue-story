<template>
  <div
    class="loading-container"
  >
    <img
      src="../assets/download.png"
      class="loading-container__spinner"
    >
  </div>
</template>

<script>
  export default {
    name: 'Spinner'
  }
</script>

<style lang="scss" scoped>
.loading-container {
  // background: rgba(255, 255, 255, 0.5);
  position: absolute;
  display: flex;
  align-items: center;
  height: 100vh;
  z-index: 1;
  left: 0;
  right: 0;

  &__spinner {
    animation: 3s rotation infinite linear;
    width: 50px;
    margin: auto;
  }
}

@keyframes rotation {
  to { transform: rotate(360deg); }
}
</style>