<template>
  <div class="branding-container">
    <img
      :src="storyData.content.logo_top_left_img"
      class="branding-container__logo"
    >
    <p
      class="branding-container__brand"
    >
      {{ storyData.content.logo_top_left_title }}
    </p>
    <img
      :src="soundIcon"
      class="branding-container__sound-icon"
      @click="toggleSound"
    >
  </div>
</template>

<script>
  export default {
    name: 'Branding',
    props: {
      storyData: {
        type: Object,
        default: function() {
          return {}
        }
      },
      hasSound: {
        type: Boolean,
        default: false
      }
    },
    computed: {
      soundIcon() {
        return this.hasSound ? require('../assets/sound-on.svg') : require('../assets/sound-off.svg')
      }
    },
    methods: {
      toggleSound() {
        this.$emit('toggle-sound')
      }
    },
  }
</script>

<style lang="scss" scoped>
.branding-container {
  display: flex;
  align-items: center;

  &__logo {
    height: 40px;
    width: 40px;
    border-radius: 50%;
    margin-right: 8px;
  }

  &__brand {
    color: white;
    font-weight: bold;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    margin-right: 24px;
  }

  &__sound-icon {
    height: 20px;
    width: 20px;
  }
}
</style>