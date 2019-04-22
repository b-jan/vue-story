<template>
  <nav
    class="navigation-container"
    :style="{ gridTemplateColumns: 'repeat(' + storyLength + ', 1fr)'}"
  >
    <div
      v-for="storyNumber in storyLength"
      :key="storyNumber"
      class="navigation-container__navigation"
    >
      <div
        :class="storyNumber - 1 < activeMediaIndex ? 'navigation__have-watched' : 'navigation__will-watch'"
      />
      <div
        v-if="storyNumber - 1 === activeMediaIndex"
        :class="'navigation__is-watching'"
        :style="{ width: videoCurrentTime + '%'}"
      />
    </div>
  </nav>
</template>

<script>
  export default {
    name: 'Navigation',
    props: {
      storyLength: {
        type: Number,
        default: 0
      },
      activeMediaIndex: {
        type: Number,
        default: 0
      },
      videoCurrentTime: {
        type: Number,
        default: 0
      }
    },
  }
</script>

<style lang="scss" scoped>
.navigation-container {
  box-sizing: border-box;
  display: grid;
  grid-column-gap: 2px;
  padding: 0 16px;

  &__navigation {
    border-radius: 4px;
    height: 4px;
    position: relative;
    width: 100%;

    .navigation {
      &__will-watch {
        background: rgba(255, 255, 255, 0.35);
        position: absolute;
        border-radius: 4px;
        height: 4px;
        width: 100%;
      }

      &__have-watched {
        background: white;
        position: absolute;
        border-radius: 4px;
        height: 4px;
        width: 100%;
      }

      &__is-watching {
        background: white;
        position: absolute;
        border-radius: 4px;
        height: 4px;
        -webkit-transition: width .1s linear;
        transition: width .1s linear;
        width: auto;
        will-change: width;
      }
    }
  }
}
</style>