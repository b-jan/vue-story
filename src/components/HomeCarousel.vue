<template>
  <div class="carousel-container">
    <Carousel
      :per-page="1"
      :value="activeStoryIndex"
      :mouse-drag="true"
      :pagination-enabled="false"
      centerMode
      @page-change="changeStory"
    >
      <Slide
        v-for="(storyData, index) in stories"
        :key="index"
      >
        <div
          v-if="index === activeStoryIndex"
          class="carousel-container__media-container"
          @click="() => goToNextMedia(index)"
        >
          <div v-show="isMediaPairActive">
            <img
              v-if="mediaPair.type === 'image'"
              :src="mediaPair.src"
              class="carousel-container__image"
            >
            <video
              v-if="mediaPair.type === 'video'"
              :poster="mediaPair.thumbnail"
              :src="mediaPair.mp4"
              autoplay
              defaultMuted
              muted
              webkit-playsinline="true"
              playsinline="true"
              class="carousel-container__video"
            />
          </div>
          <div v-show="isMediaOddActive">
            <img
              v-if="mediaOdd.type === 'image'"
              :src="mediaOdd.src"
              class="carousel-container__image"
            >
            <video
              v-if="mediaOdd.type === 'video'"
              :poster="mediaOdd.thumbnail"
              :src="mediaOdd.mp4"
              autoplay
              defaultMuted
              muted
              webkit-playsinline="true"
              playsinline="true"
              class="carousel-container__video"
            />
          </div>
        </div>
      </Slide>
    </Carousel>
  </div>
</template>

<script>
import { Carousel, Slide } from 'vue-carousel';

export default {
  name: 'HomeCarousel',
  components: {
    Carousel,
    Slide
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
      activeMediaIndex: 0
    }
  },
  computed: {
    storiesLength() {
      return this.stories.map(storyData => {
        return storyData.content.story.length
      })
    },
    isMediaPairActive() {
      return (this.activeMediaIndex + 1) % 2
    },
    isMediaOddActive() {
      return this.activeMediaIndex % 2
    },
    currentMedia() {
      return this.stories[this.activeStoryIndex].content.story[this.activeMediaIndex]
    },
    nextMedia() {
      return this.stories[this.activeStoryIndex].content.story[this.activeMediaIndex + 1]
    },
    nextStoryFirstMedia() {
      return this.stories[this.activeStoryIndex + 1].content.story[0]
    },
    mediaPair() {
      if (this.stories.length === 0) {
        return {}
      }
      if (this.isMediaPairActive)
        return this.currentMedia
      if (this.isMediaOddActive && this.nextMedia)
        return this.nextMedia

      if (this.nextStoryFirstMedia)
        return this.nextStoryFirstMedia

      return {}
    },
    mediaOdd() {
      if (this.stories.length === 0) {
        return {}
      }
      if (this.isMediaOddActive)
        return this.currentMedia
      if (this.isMediaPairActive && this.nextMedia)
        return this.nextMedia

      if (this.nextStoryFirstMedia)
        return this.nextStoryFirstMedia

      return {}
    }
  },
  methods: {
    storyMedia(storyData) {
      return storyData.content.story
    },
    changeStory(storyNumber) {
      this.activeStoryIndex = storyNumber
    },
    goToNextMedia: function(index) {
      this.activeMediaIndex += 1

      // On last slide, go on next story, on first slide
      if (this.activeMediaIndex === this.storiesLength[index]) {
        this.activeMediaIndex = 0
        this.activeStoryIndex += 1
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.carousel-container {
  display: flex;
  flex-direction: column;

  &__media-container {
    width: 100%;
    object-fit: cover;
    object-position: 75% 50%;
  }

  &__image {
    width: 100%;
    display: flex;
    object-fit: cover;
    object-position: 75% 50%;
  }

  &__video {
    width: 100%;
    display: flex;
    object-fit: cover;
    object-position: 75% 50%;
  }
}

/*
This part overrides the vue-carousel style
*/
.VueCarousel {
  height: 100%;
  overflow: hidden;
}

.VueCarousel-wrapper {
  overflow: visible !important;
}

.VueCarousel-inner {
  position: relative;
  transition-duration: 0s !important;
  transform: none !important;
}

.VueCarousel-slide {
  transition: opacity 0.8s ease-out;
  width: 100%;
  height: 100%;
}

.VueCarousel-slide-current {
  opacity: 1;
  transition: opacity 0.8s ease-in;
}

</style>
