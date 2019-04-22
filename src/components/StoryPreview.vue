<template>
  <div
    class="story-container"
  >
    <MediaPreview
      :media="media"
      class="story-container__media-container"
    />
    <Navigation
      :story-length="storyLength"
      :active-media-index="mediaIndex"
      class="story-container__navigation-container"
    />
  </div>
</template>

<script>
import MediaPreview from './MediaPreview.vue'
import Navigation from './Navigation.vue'

  export default {
    name: 'StoryPreview',
    components: {
      MediaPreview,
      Navigation
    },
    props: {
      storyLength: {
        type: Number,
        default: 0
      },
      mediaIndex: {
        type: Number,
        default: 0
      },
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
.story-container {
  &__media-container {
    position: absolute;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    left: 0;
    right: 0;
    margin: auto;
  }

  &__navigation-container {
    position: fixed;
    z-index: 4;
    top: 16px;
    width: 100%;
  }
}
</style>