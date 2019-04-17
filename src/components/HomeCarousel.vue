<template>
  <div class="carousel-container">
    <Carousel
      :per-page="1"
      :value="activeSlide"
      :mouse-drag="true"
      :pagination-enabled="false"
      autoplay
      centerMode
    >
      <Slide
        v-for="(videoLink, index) in videoLinks"
        :class="{'VueCarousel-slide-current': activeSlide === index}"
        :key="index"
      >
        <img
          v-if="videoLink.type === 'image'"
          :src="videoLink.src"
          class="carousel-container__image"
        >
        <video
          v-if="videoLink.type === 'video'"
          ref="video"
          :src="videoLink.src"
          autoplay
          muted
          @timeupdate="() => goToNextSlide(index)"
          @canplay="() => waitAndRemoveSplashScreen(index)"
        />
      </Slide>
    </Carousel>
    <div
      v-if="hasVideoStarted"
      class="pagination"
    >
      <button
        v-for="(videoLink, index) in videoLinks"
        :key="index"
        :class="{'pagination__dot--current': index === activeSlide}"
        class="pagination__dot"
        @click="changeSlide(index)"
      />
    </div>
  </div>
</template>

<script>
import { Carousel, Slide } from 'vue-carousel';

const VIDEO_ENDING_OFFSET = 0.6

export default {
  name: 'HomeCarousel',
  components: {
    Carousel,
    Slide
  },
  props: {
    videoLinks: {
      default: function() {
        return []
      },
      type: Array
    }
  },
  data() {
    return {
      activeSlide: 0,
      hasVideoStarted: false
    }
  },
  computed: {
    carouselLength() {
      return this.videoLinks.length
    }
  },
  methods: {
    waitAndRemoveSplashScreen: function(index) {
      if (index !== 0) return
      setTimeout(() => {
        this.hasVideoStarted = true
        this.$emit('removeSplashScreen')
      }, 1000) // wait 1 sec because of videos starting glitch
    },
    goToNextSlide: function(index) {
      // Go to next slide only if the video is nearly completed: 0.6s (vue-carousel transition lasts 0.8s)

      // During a page change, vue refs are removed. Leave the slide change when video refs are removed
      if (0 === this.$refs.video.length) return

      if (
        this.$refs.video[this.activeSlide].currentTime <
        this.$refs.video[this.activeSlide].duration - VIDEO_ENDING_OFFSET
      )
        return
      // Only listen end event for active video
      if (this.activeSlide === index) {
        this.activeSlide += 1
        // On last slide, loop on first slide
        if (this.activeSlide === this.carouselLength) {
          this.activeSlide = 0
        }
        this.replayCurrentVideo()
      }
    },
    changeSlide: function(slideNumber) {
      this.activeSlide = slideNumber
      this.replayCurrentVideo()
    },
    replayCurrentVideo: function() {
      this.$refs.video[this.activeSlide].currentTime = 0
      this.$refs.video[this.activeSlide].play()
    }
  }
}
</script>

<style lang="scss" scoped>
.carousel-container {
  display: flex;
  flex-direction: column;

  &__image {
    width: 100%;
    object-fit: cover;
    object-position: 75% 50%;
  }
}

.pagination {
  align-self: center;
  margin-top: 32px;

  &__dot {
    width: 6px;
    height: 6px;
    padding: 0;
    background-color: white;
    border-radius: 50%;
    margin: 0 5px;
    border: none;
    outline: none;
    cursor: pointer;

    &--current {
      background-color: blue;
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
