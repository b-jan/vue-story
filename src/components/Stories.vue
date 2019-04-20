<template>
  <div class="stories-container">
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
          @click="event => changeMedia(index, event)"
        >
          <Media
            v-show="isMediaPairActive"
            :media="mediaPair"
          />
          <Media
            v-show="isMediaOddActive"
            :media="mediaOdd"
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
      storiesLatestMediaIndex: []
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
      return this.stories[this.activeStoryIndex].content.story[
        this.activeMediaIndex
      ]
    },
    nextMedia() {
      return this.stories[this.activeStoryIndex].content.story[
        this.activeMediaIndex + 1
      ]
    },
    previousStoryLatestMedia() {
      return this.stories[this.activeStoryIndex - 1].content.story[
        this.storiesLatestMediaIndex[this.activeStoryIndex - 1]
      ]
    },
    nextStoryLatestMedia() {
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
    }
  },
  methods: {
    storyMedia(storyData) {
      return storyData.content.story
    },
    setStoryLatestMedia() {
      this.storiesLatestMediaIndex[this.activeStoryIndex] = this.activeMediaIndex
    },
    changeStory(storyIndex) {
      this.setStoryLatestMedia()
      this.activeMediaIndex = this.storiesLatestMediaIndex[storyIndex]
      this.activeStoryIndex = storyIndex
    },
    changeMedia: function(index, event) {
      const containerWidth = this.$refs['media-container'][0].clientWidth
      const x = event.clientX

      if (containerWidth / 2 > x) {
        // On first media, go on previous story, on latest media
        if (this.activeMediaIndex === 0 && this.activeStoryIndex > 0) {
          this.changeStory(this.activeStoryIndex - 1)
        } else {
          this.activeMediaIndex -= 1
        }
      } else {
        // On last media, go on next story, on latest media
        if (
          this.activeMediaIndex === this.storiesLength[index] - 1 &&
          this.activeStoryIndex < this.stories.length
        ) {
          this.changeStory(this.activeStoryIndex + 1)
        } else {
          this.activeMediaIndex += 1
        }
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.stories-container {
  display: flex;
  flex-direction: column;
}

</style>
