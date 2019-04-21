<template>
  <div class="stories-container">
    <!-- <keep-alive
      v-if="!isCurrentMediaActive"
    >
      <Spinner />
    </keep-alive> -->
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
          class="stories-container__media-container"
        />
        <!-- <MediaPreview
          v-if="index === activeStoryIndex && activeMediaIndex > 0"
          :media="previousMedia"
          class="stories-container__media-container"
        /> -->
        <div
          v-if="index === activeStoryIndex"
          ref="media-container"
          :style="{zIndex: 1}"
          class="stories-container__media-container"
          @click="event => changeMedia(event)"
        >
          <Media
            v-if="mediaAlpha"
            :media="mediaAlpha"
            :active="isMediaAlphaActive"
            position="Alpha"
            :style="{zIndex: isMediaAlphaActive ? 2 : 1}"
            class="stories-container__media-container"
            @end-video="goToNextMedia"
            @remove-loading="removeLoading"
          />
          <Media
            v-if="mediaBeta"
            :media="mediaBeta"
            :active="isMediaBetaActive"
            position="Beta"
            :style="{zIndex: isMediaBetaActive ? 2 : 1}"
            class="stories-container__media-container"
            @end-video="goToNextMedia"
            @remove-loading="removeLoading"
          />
          <Media
            v-if="mediaGamma"
            :media="mediaGamma"
            :active="isMediaGammaActive"
            position="Gamma"
            :style="{zIndex: isMediaGammaActive ? 2 : 1}"
            class="stories-container__media-container"
            @end-video="goToNextMedia"
            @remove-loading="removeLoading"
          />
        </div>
        <!-- <MediaPreview
          v-if="index === activeStoryIndex && activeMediaIndex < storiesLength[activeStoryIndex] - 1"
          :media="nextMedia"
          class="stories-container__media-container"
        /> -->
        <MediaPreview
          v-if="index === activeStoryIndex + 1"
          :media="nextStoryLatestMedia"
          class="stories-container__media-container"
        />
      </Slide>
    </Carousel>
  </div>
</template>

<script>
import Spinner from './Spinner.vue'
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

      mediaAlphaIndex: -1,
      mediaBetaIndex: 0,
      mediaGammaIndex: 1,
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
    isMediaAlphaActive() {
      return (this.activeMediaIndex + 1) % 3 === 0
    },
    isMediaBetaActive() {
      return this.activeMediaIndex % 3 === 0
    },
    isMediaGammaActive() {
      return (this.activeMediaIndex - 1) % 3 === 0
    },
    mediaAlpha() {
      if (
        this.stories[this.activeStoryIndex]
        && this.stories[this.activeStoryIndex].content.story[this.mediaAlphaIndex]
      )
        return this.stories[this.activeStoryIndex].content.story[
          this.mediaAlphaIndex
        ]
      return null
    },
    mediaBeta() {
      if (
        this.stories[this.activeStoryIndex]
        && this.stories[this.activeStoryIndex].content.story[this.mediaBetaIndex]
      )
        return this.stories[this.activeStoryIndex].content.story[
          this.mediaBetaIndex
        ]
      return null
    },
    mediaGamma() {
      if (
        this.stories[this.activeStoryIndex]
        && this.stories[this.activeStoryIndex].content.story[this.mediaGammaIndex]
      )
        return this.stories[this.activeStoryIndex].content.story[
          this.mediaGammaIndex
        ]
      return null
    },
    previousMedia() {
      if (
        this.stories[this.activeStoryIndex]
        && this.stories[this.activeStoryIndex].content.story[this.activeMediaIndex - 1]
      )
        return this.stories[this.activeStoryIndex].content.story[
          this.activeMediaIndex - 1
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
      if (
        this.stories[this.activeStoryIndex]
        && this.stories[this.activeStoryIndex].content.story[this.activeMediaIndex + 1]
      )
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
      if (this.activeStoryIndex === this.stories.length - 1) {
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
      this.setStoryLatestMedia()
    }
  },
  methods: {
    storyMedia(storyData) {
      return storyData.content.story
    },
    removeLoading(position) {
      this[`isMedia${position}Active`] = true
    },
    setStoryLatestMedia(index) {
      this.storiesLatestMediaIndex[this.activeStoryIndex] = this.activeMediaIndex
    },
    changeStory(storyIndex) {
      this.activeMediaIndex = this.storiesLatestMediaIndex[storyIndex]
      this.mediaAlphaIndex = this.activeMediaIndex - 1
      this.mediaBetaIndex = this.activeMediaIndex
      this.mediaGammaIndex = this.activeMediaIndex + 1

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
        this.mediaGammaIndex = this.isMediaBetaActive ? this.mediaGammaIndex - 3 : this.mediaGammaIndex
        this.mediaBetaIndex = this.isMediaAlphaActive ? this.mediaBetaIndex - 3 : this.mediaBetaIndex
        this.mediaAlphaIndex = this.isMediaGammaActive ? this.mediaAlphaIndex - 3 : this.mediaAlphaIndex
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
        this.mediaAlphaIndex = this.isMediaBetaActive ? this.mediaAlphaIndex + 3 : this.mediaAlphaIndex
        this.mediaBetaIndex = this.isMediaGammaActive ? this.mediaBetaIndex + 3 : this.mediaBetaIndex
        this.mediaGammaIndex = this.isMediaAlphaActive ? this.mediaGammaIndex + 3 : this.mediaGammaIndex
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
}

.VueCarousel {
  height: 100vh;
}

.VueCarousel-slide {
  position: relative;
  height: 100vh;
}

</style>
