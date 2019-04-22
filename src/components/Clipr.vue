<template>
  <div
    class="clipr-container"
  >
    <Stories
      :stories="stories"
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
