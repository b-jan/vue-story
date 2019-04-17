<template>
  <div class="carousel-container">
    <Carousel
      :per-page="1"
      :value="activeStory"
      :mouse-drag="true"
      :pagination-enabled="false"
      centerMode
      @page-change="changeStory"
    >
      <Slide
        v-for="(storyData, index1) in stories"
        :class="{'VueCarousel-slide-current': activeMedia === index1}"
        :key="index1"
      >
        <div
          v-for="(story, index2) in storyMedia(storyData)"
          :key="story.src"
          class="carousel-container__media-container"
          @click="() => goToNextMedia(index1, story.type)"
        >
          <img
            v-if="story.type === 'image'"
            :src="story.src"
            :class="{'carousel-container__image--current': index2 === activeMedia}"
            class="carousel-container__image"
          >
          <video
            v-if="story.type === 'video'"
            ref="video"
            :src="story.mp4"
            autoplay
            muted
            @timeupdate="() => goToNextMedia(index2)"
            :class="{'carousel-container__video--current': index2 === activeMedia}"
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
      activeStory: 0,
      activeMedia: 0,
      hasVideoStarted: false
    }
  },
  computed: {
    storiesLength() {
      return this.stories.map(storyData => {
        return storyData.content.story.length
      })
    }
  },
  methods: {
    storyMedia(storyData) {
      return storyData.content.story
    },
    changeStory(storyNumber) {
      this.activeStory = storyNumber
    },
    goToNextMedia: function(index1, type) {
      if (type === 'image') {
        this.activeMedia += 1
      }

      if (type === 'video') {
        this.goToNextVideo()
      }

      // On last slide, go on next story, on first slide
      if (this.activeMedia === this.storiesLength[index1]) {
        this.activeMedia = 0
        this.activeStory += 1
      }
    },
    goToNextVideo: function() {
      this.activeMedia += 1
      this.replayCurrentVideo()
    },
    replayCurrentVideo: function() {
      this.$refs.video[this.activeMedia].currentTime = 0
      this.$refs.video[this.activeMedia].play()
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
    display: none;
    object-fit: cover;
    object-position: 75% 50%;

    &--current {
      display: flex;
    }
  }

  &__video {
    width: 100%;
    display: none;
    object-fit: cover;
    object-position: 75% 50%;

    &--current {
      display: flex;
    }
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
