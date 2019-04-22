<template>
  <div>
    <img
      :src="storyData.content.logo_top_left_img"
      class="branding-container__logo"
    >
    <p
      class="branding-container__brand"
    >
      {{ storyData.content.logo_top_left_title }}
    </p>
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
      }
    }
  }
</script>

<style lang="scss" scoped>
.branding-container {
  &__logo {
    height: 40px;
    width: 40px;
    border-radius: 50%;
    margin-right: 8px;
  }

  &__brand {
    color: white;
    font-weight: bold;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif
  }
}
</style>