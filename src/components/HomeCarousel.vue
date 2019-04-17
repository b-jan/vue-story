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
        :class="{'VueCarousel-slide-current': activeMediaIndex === index}"
        :key="index"
      >
        <div
          class="carousel-container__media-container"
          @click="() => goToNextMedia(index, story.type)"
        >
          <img
            v-if="activeMedia && activeMedia.type === 'image'"
            :src="activeMedia.src"
            class="carousel-container__image"
          >
          <video
            v-if="activeMedia && activeMedia.type === 'video'"
            :poster="activeMedia.thumbnail"
            ref="video"
            :src="activeMedia.mp4"
            autoplay
            muted
            @timeupdate="() => goToNextVideo()"
            class="carousel-container__video"
          />
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
      activeMediaIndex: 0,
      hasVideoStarted: false
    }
  },
  computed: {
    storiesLength() {
      return this.stories.map(storyData => {
        return storyData.content.story.length
      })
    },
    activeMedia() {
      if (this.stories.length > 0) {
        return this.stories[this.activeStoryIndex].content.story[this.activeMediaIndex]
      }
      return undefined
    }
  },
  mounted() {
  },
  methods: {
    storyMedia(storyData) {
      return storyData.content.story
    },
    changeStory(storyNumber) {
      this.activeStoryIndex = storyNumber
    },
    goToNextMedia: function(index, type) {
      if (type === 'image') {
        this.activeMediaIndex += 1
      }

      if (type === 'video') {
        this.goToNextVideo()
      }

      // On last slide, go on next story, on first slide
      if (this.activeMediaIndex === this.storiesLength[index]) {
        this.activeMediaIndex = 0
        this.activeStoryIndex += 1
      }
    },
    goToNextVideo: function() {
      this.activeMediaIndex += 1
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
