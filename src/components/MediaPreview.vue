<template>
  <div
    class="thumbnail-container"
  >
    <img
      :src="media.thumbnail || media.src"
      class="thumbnail-container__image"
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
.thumbnail-container {
  width: 100%;
  object-fit: cover;

  &__image {
    width: 100%;
    display: flex;
    object-fit: cover;
  }
}
</style>
