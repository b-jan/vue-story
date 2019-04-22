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
      stories: [],

      touchEvents: {},
      userAgentInfo: {},
      lastTouch: 0
    }
  },
  mounted () {
    this.axios
      .get('https://wrapapi.com/use/baptistej/clipr/magsante/0.0.5?wrapAPIKey=Wa80vbUnmYXvnj48BMMyOeMCSW3zwg81')
      .then(response => {
        this.logo = response.data.data.output.loading_logo
        this.stories = response.data.data.output.stories
        this.touchEvents = response.data.data.output.touch_events
        this.userAgentInfo = response.data.data.output.user_agent_info

        document.addEventListener(this.touchEvents.touchStartEventName, this.preventZoom, {passive:false})
        document.addEventListener(this.touchEvents.touchSliderEvents.move, this.resetPreventZoom, {passive:false})
        window.addEventListener(this.touchEvents.touchSliderEvents.move, this.preventMotion, {passive:false});
      })
  },
  methods: {
    preventZoom(e) {
      const t2 = e.timeStamp
      const t1 = this.lastTouch || t2
      const dt = t2 - t1
      const fingers = typeof e.touches != "undefined" ? e.touches.length : 1
      this.lastTouch = t2

      if (!dt || dt >= 500 || fingers > 1) {
        return
      }

      this.resetPreventZoom()

      if (typeof e.target.getAttribute == "function"
        && e.target.getAttribute('type') != "checkbox") {
        e.target.click()
        e.preventDefault()
      }
    },
    resetPreventZoom() {
      this.lastTouch = 0
    },
    preventMotion(e) {
      if (typeof e !== "undefined" && e != null) {
        e.preventDefault()
        e.stopPropagation()
      }
    }
  }
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
