<template>
  <div class="color-picker">
    <div class="color-picker__left">
      <span class="color-picker__title">Color picker</span>
      <WheelColorPicker v-model="pickedColor"/>
      <span class="color-picker__current" :style="{background:pickedColor, color: currentTextColor}">{{pickedColor}}</span>
    </div>
  </div>
</template>

<script>
import WheelColorPicker from './WheelColorPicker'
import tinycolor from 'tinycolor2'

export default {
  data() {
    return {
      pickedColor: 'rgb(255,90,241)',
    }
  },
  computed: {
    currentTextColor() {
      return tinycolor(this.pickedColor).isLight() ? '#000' : '#fff'
    }
  },
  components: {
    WheelColorPicker
  },
}
</script>

<style lang="stylus" scoped>
.color-picker
  display inline-flex
  box-shadow: 0 3px 6px 0 rgba(0,0,0,.1), 0 1px 3px 0 rgba(0,0,0,.08);
  border-radius 8px
  margin 1em
  padding 2em

  &__left
    display flex
    flex-direction column
    justify-content: space-between;

  &__title
    margin-bottom 1em

  &__current
    border-radius 8px
    padding .5em
    margin-top 1em
    transition color .2s ease-in

</style>

