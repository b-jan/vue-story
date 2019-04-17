<template>
  <div class="home-container">
    <HomeCarousel
      :video-links="videoLinks"
    />
  </div>
</template>

<script>
import HomeCarousel from './HomeCarousel.vue'

export default {
  name: 'Home',
  components: {
    HomeCarousel
  },
  data() {
    return {
      logo: '',
      stories: []
    }
  },
  computed: {
    videoLinks() {
      if (this.stories.length === 0) return []
      return this.stories[1].content.story
    }
  },
  mounted () {
    this.axios
      .get('https://wrapapi.com/use/baptistej/clipr/magsante/0.0.1?wrapAPIKey=Wa80vbUnmYXvnj48BMMyOeMCSW3zwg81')
      .then(response => {
        this.logo = response.data.data.output.loading_logo
        this.stories = response.data.data.output.stories
      })
  }
}
</script>

<style scoped>
.home-container {
  background: white;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
}
</style>
