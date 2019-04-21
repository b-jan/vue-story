<template>
  <div
    class="preview-container"
  >
    <img
      :src="media.thumbnail || media.src"
      class="preview-container__image"
    >
  </div>
</template>

<script>
export default {
  name: "MediaPreview",
  props: {
    media: {
      type: Object,
      default: function() {
        return {}
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.preview-container {
  width: 100%;
  object-fit: cover;

  &__image {
    width: 100%;
    display: flex;
    object-fit: cover;
  }
}
</style>
