<template>
  <div
    class="redirection-button"
  >
    <p
      class="redirection-button__above-title"
    >
      {{ buttonInfo.aboveTitle }}
    </p>
    <a
      :href="buttonInfo.url"
      :style="{ backgroundColor: buttonInfo.color.static }"
      class="redirection-button__button"
    >
      {{ buttonInfo.title }}
    </a>
  </div>
</template>

<script>
  export default {
    name: 'RedirectionButton',
    props: {
      buttonInfo: {
        type: Object,
        default: function() {
          return {}
        }
      }
    }
  }
</script>

<style lang="scss" scoped>
.redirection-button {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;

  &__above-title {
    color: white;
    font-weight: bold;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
  }

  &__button {
    color: white;
    font-weight: bold;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    text-decoration: none;
    border-radius: 20px;
    height: 40px;
    width: 90%;
    display: flex;
    align-items: center;
    justify-content: center;
  }
}
</style>