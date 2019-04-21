<template>
  <div
    class="clipr-container"
  >
    <Stories
      :stories="stories"
      @touchend="handleTouch"
      @touchMove="handleTouch"
    />
  </div>
</template>

<script>
import Stories from './Stories.vue'

export default {
  name: 'Clipr',
  components: {
    Stories
  },
  data() {
    return {
      logo: '',
      stories: []
    }
  },
  mounted () {
    this.axios
      .get('https://wrapapi.com/use/baptistej/clipr/magsante/0.0.5?wrapAPIKey=Wa80vbUnmYXvnj48BMMyOeMCSW3zwg81')
      .then(response => {
        this.logo = response.data.data.output.loading_logo
        this.stories = response.data.data.output.stories
      })
    this.handleScroll()
  },
  methods: {
    handleTouch(event) {
      event.preventDefault()
      const body = document.documentElement;
      if (body.requestFullscreen) {
        body.requestFullscreen();
      } else if (body.webkitrequestFullscreen) {
        body.webkitrequestFullscreen();
      } else if (body.mozrequestFullscreen) {
        body.mozrequestFullscreen();
      } else if (body.msrequestFullscreen) {
        body.msrequestFullscreen();
      }
    },
    handleScroll() {
      window.addEventListener("load", function() { window. scrollTo(0, 1); });
    }
  },
}
</script>

<style lang="scss" scoped>
.clipr-container {
  background: black;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
}
</style>
