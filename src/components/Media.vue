<template>
  <div
    class="media-container"
  >
    <img
      v-if="media.type === 'image'"
      :src="media.src"
      decoding="sync"
      class="media-container__image"
    >
    <video
      v-if="media.type === 'video'"
      ref="video"
      :poster="media.thumbnail"
      :src="videoSource"
      preload="auto"
      autoplay
      defaultMuted
      muted
      playsinline
      webkit-playsinline
      class="media-container__video"
      @timeupdate="handleTimeUpdate"
      @loadedmetadata="beReadyToStart"
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
    },
    active: {
      type: Boolean,
      default: false
    },
    isSoundActive: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    videoSource() {
      // const video = this.$refs.video
      // if (video.canPlayType('video/webm') === 'probably')
      //   return this.media.webm
      return this.media.mp4
    }
  },
  watch: {
    active(newValue) {
      if (!this.$refs.video) return

      const video = this.$refs.video
      if (!newValue) {
        video.pause()
      }
      if (newValue) {
        video.currentTime = 0
        video.play()
      }
    },
    isSoundActive(newIsSoundActive) {
      this.activateSound(newIsSoundActive)
    }
  },
  mounted() {
    this.activateSound(this.isSoundActive)
  },
  methods: {
    handleTimeUpdate() {
      if (!this.$refs.video) return

      const video = this.$refs.video
      if (this.active) this.$emit('update-current-time', 100 * video.currentTime / video.duration)

      if (!video.duration || video.currentTime < video.duration) return

      this.$emit('end-video')
      video.currentTime = 0
    },
    beReadyToStart() {
      if (!this.$refs.video) return

      const video = this.$refs.video
      // video.currentTime = 0
      // video.pause()

      if (this.active) video.play()
    },
    activateSound(newIsSoundActive) {
      const video = this.$refs.video
      if (video.muted != !newIsSoundActive) {
        video.muted = !newIsSoundActive
      }
    }
  },
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
