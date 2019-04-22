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
    <RedirectionButton
      :button-info="buttonInfo"
      class="story-container__cta-container"
    />
  </div>
</template>

<script>
import MediaPreview from './MediaPreview.vue'
import Navigation from './Navigation.vue'
import RedirectionButton from './RedirectionButton.vue'

  export default {
    name: 'StoryPreview',
    components: {
      MediaPreview,
      Navigation,
      RedirectionButton
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
      },
      buttonInfo: {
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

  &__cta-container {
    position: fixed;
    z-index: 4;
    bottom: 24px;
  }
}
</style>