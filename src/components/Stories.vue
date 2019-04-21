<template>
  <div class="stories-container">
    <keep-alive
      v-if="!isVideoReady"
    >
      <div
        class="loading-container"
      >
        <img
          src="../assets/download.png"
          class="loading-container__spinner"
        >
      </div>
    </keep-alive>
    <Carousel
      :per-page="1"
      :value="activeStoryIndex"
      :mouse-drag="true"
      :pagination-enabled="false"
      @page-change="changeStory"
    >
      <Slide
        v-for="(storyData, index) in stories"
        :key="storyData.header_title + index"
      >
        <MediaPreview
          v-if="index === activeStoryIndex - 1"
          :media="previousStoryLatestMedia"
          class="stories-container__media"
        />
        <MediaPreview
          v-if="index === activeStoryIndex && previousMediaIndex > -1"
          :style="{zIndex: previousMediaZIndex}"
          :media="previousMedia"
          class="stories-container__media"
        />
        <div
          v-if="index === activeStoryIndex"
          ref="media-container"
          :style="{zIndex: 1}"
          class="stories-container__media"
          @click="event => changeMedia(event)"
        >
          <Media
            :media="currentMedia"
            :active="isVideoReady"
            @end-video="goToNextMedia"
            @remove-loading="removeLoading"
          />
        </div>
        <MediaPreview
          v-if="index === activeStoryIndex && nextMediaIndex < storiesLength[activeStoryIndex]"
          :style="{zIndex: nextMediaZIndex}"
          :media="nextMedia"
          class="stories-container__media"
        />
        <MediaPreview
          v-if="index === activeStoryIndex + 1"
          :media="nextStoryLatestMedia"
          class="stories-container__media"
        />
      </Slide>
    </Carousel>
  </div>
</template>

<script>
import { Carousel, Slide } from 'vue-carousel'
import Media from './Media.vue'
import MediaPreview from './MediaPreview.vue'

export default {
  name: 'Stories',
  components: {
    Carousel,
    Slide,
    Media,
    MediaPreview
  },
  props: {
    stories: {
      type: Array,
      default: function() {
        return []
      }
    }
  },
  data() {
    return {
      activeStoryIndex: 0,
      activeMediaIndex: 0,
      storiesLatestMediaIndex: [],
      previousMediaIndex: -1,
      nextMediaIndex: 1,
      previousMediaZIndex: 0,
      nextMediaZIndex: 0,
      isVideoReady: false
    }
  },
  computed: {
    globalLength() {
      return this.stories.length
    },
    storiesLength() {
      return this.stories.map(storyData => {
        return storyData.content.story.length
      })
    },
    previousMedia() {
      if (
        this.stories[this.activeStoryIndex]
        && this.stories[this.activeStoryIndex].content.story[this.previousMediaIndex]
      )
        return this.stories[this.activeStoryIndex].content.story[
          this.previousMediaIndex
        ]
      return null
    },
    currentMedia() {
      if (
        this.stories[this.activeStoryIndex]
        && this.stories[this.activeStoryIndex].content.story[this.activeMediaIndex]
      )
        return this.stories[this.activeStoryIndex].content.story[
          this.activeMediaIndex
        ]
      return null
    },
    nextMedia() {
      if (this.stories[this.activeStoryIndex])
        return this.stories[this.activeStoryIndex].content.story[
          this.nextMediaIndex
        ]
      return null
    },
    previousStoryLatestMedia() {
      // When first story, do not load any media
      if (this.activeStoryIndex === 0) {
        return {}
      }
      return this.stories[this.activeStoryIndex - 1].content.story[
        this.storiesLatestMediaIndex[this.activeStoryIndex - 1]
      ]
    },
    nextStoryLatestMedia() {
      // When first story, do not load any media
      if (
        this.activeStoryIndex === this.stories.length - 1
      ) {
        return {}
      }
      return this.stories[this.activeStoryIndex + 1].content.story[
        this.storiesLatestMediaIndex[this.activeStoryIndex + 1]
      ]
    }
  },
  watch: {
    globalLength(length) {
      if (length === 0) return
      // initiate stories latest media indexes state with a table of 0
      this.storiesLatestMediaIndex.length = length
      this.storiesLatestMediaIndex.fill(0)
    },
    currentMedia(newMedia) {
      if (!newMedia || (newMedia.type === 'video' && newMedia.duration === 0)) {
        this.goToNextMedia()
        return
      }
      this.previousMediaIndex = this.activeMediaIndex - 1
      this.previousMediaZIndex = 0
      this.nextMediaIndex = this.activeMediaIndex + 1
      this.nextMediaZIndex = 0

      this.setStoryLatestMedia()
    }
  },
  methods: {
    storyMedia(storyData) {
      return storyData.content.story
    },
    removeLoading() {
      this.isVideoReady = true
    },
    setStoryLatestMedia() {
      this.storiesLatestMediaIndex[this.activeStoryIndex] = this.activeMediaIndex
    },
    changeStory(storyIndex) {
      this.activeMediaIndex = this.storiesLatestMediaIndex[storyIndex]
      this.activeStoryIndex = storyIndex
    },
    changeMedia: function(event) {
      this.isVideoReady = false
      const containerWidth = this.$refs['media-container'][0].clientWidth
      const x = event.clientX

      if (containerWidth / 2 > x) {
        this.goToPreviousMedia()
      } else {
        this.goToNextMedia()
      }
    },
    goToPreviousMedia() {
      // When first media, go on previous story, on the latest media
      if (this.activeMediaIndex === 0 && this.activeStoryIndex > 0) {
        this.changeStory(this.activeStoryIndex - 1)
      } else {
        this.previousMediaZIndex = 2
        this.activeMediaIndex -= 1
      }
    },
    goToNextMedia() {
      // When last media, go on next story, on the latest media
      if (
        this.activeMediaIndex === this.storiesLength[this.activeStoryIndex] - 1 &&
        this.activeStoryIndex < this.stories.length
      ) {
        this.changeStory(this.activeStoryIndex + 1)
      } else {
        this.nextMediaZIndex = 2
        this.activeMediaIndex += 1
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.stories-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: 100vh;
  position: relative;

  &__media {
    z-index: -1;
    position: absolute;
    display: flex;
    align-items: center;
    height: 100vh;
    left: 0;
    right: 0;
    margin: auto;
  }
}

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

.VueCarousel {
  height: 100vh;
}

.VueCarousel-slide {
  position: relative;
  height: 100vh;
}

</style>
