<template>
  <div class="stories-container">
    <keep-alive>
      <div
        v-if="!isVideoReady"
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
        <div
          v-if="index === activeStoryIndex"
          ref="media-container"
          @click="event => changeMedia(event)"
        >
          <Media
            v-show="isMediaPairActive"
            :media="mediaPair"
            :active="isMediaPairActive"
            @end-video="goToNextMedia"
            @remove-loading="removeLoading"
          />
          <Media
            v-show="isMediaOddActive"
            :media="mediaOdd"
            :active="isMediaOddActive"
            @end-video="goToNextMedia"
            @remove-loading="removeLoading"
          />
        </div>
        <MediaPreview
          v-if="index === activeStoryIndex + 1"
          :media="nextStoryLatestMedia"
        />
        <MediaPreview
          v-if="index === activeStoryIndex - 1"
          :media="previousStoryLatestMedia"
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
      default: function() {
        return []
      },
      type: Array
    }
  },
  data() {
    return {
      activeStoryIndex: 0,
      activeMediaIndex: 0,
      storiesLatestMediaIndex: [],
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
    isMediaPairActive() {
      return !!((this.activeMediaIndex + 1) % 2)
    },
    isMediaOddActive() {
      return !!(this.activeMediaIndex % 2)
    },
    currentMedia() {
      if (this.stories[this.activeStoryIndex])
        return this.stories[this.activeStoryIndex].content.story[
          this.activeMediaIndex
        ]
      return null
    },
    nextMedia() {
      if (this.stories[this.activeStoryIndex])
        return this.stories[this.activeStoryIndex].content.story[
          this.activeMediaIndex + 1
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
    },
    mediaPair() {
      if (this.stories.length === 0) {
        return {}
      }
      if (this.isMediaPairActive) return this.currentMedia
      if (this.isMediaOddActive && this.nextMedia) return this.nextMedia

      if (this.nextStoryLatestMedia) return this.nextStoryLatestMedia

      return {}
    },
    mediaOdd() {
      if (this.stories.length === 0) {
        return {}
      }
      if (this.isMediaOddActive) return this.currentMedia
      if (this.isMediaPairActive && this.nextMedia) return this.nextMedia

      if (this.nextStoryLatestMedia) return this.nextStoryLatestMedia

      return {}
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
}

.loading-container {
  background:rgba(255, 255, 255, 0.5);
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
