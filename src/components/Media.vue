<template>
  <div
    class="media-container"
  >
    <img
      v-if="media.type === 'image'"
      :src="media.src"
      class="media-container__image"
    >
    <video
      v-if="media.type === 'video'"
      :poster="media.thumbnail"
      :src="media.mp4"
      autoplay
      defaultMuted
      muted
      webkit-playsinline="true"
      playsinline="true"
      class="media-container__video"
    />
  </div>
</template>

<script>
export default {
  name: "Media",
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
.media-container {
  &__image,
  &__video {
    width: 100%;
    display: flex;
    object-fit: cover;
  }
}
</style>
