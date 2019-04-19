<template>
  <div class="popup">
    <ColorPicker v-model="pickedColor"/>
    <span class="popup__current-hex">{{hex}}</span>
    <select v-model="selectedMode" class="select">
      <option>RGB</option>
      <option>HSL</option>
      <option>HSV</option>
    </select>
    <div class="popup__current-mode">
      <div v-for="(value, key) of currentMode" :key="key" class="mode-item">
        <span>{{key}}</span>
        <span>{{value}}</span>
      </div>
    </div>
    <button
      class="popup__save"
      :style="{background:pickedColor, color: currentTextColor}"
    >Сохранить</button>
  </div>
</template>

<script>
import ColorPicker from "./ColorPicker.vue";
import tinycolor from "tinycolor2";

export default {
  data() {
    return {
      pickedColor: "rgb(255,90,241)",
      selectedMode: "RGB"
    };
  },
  computed: {
    // eslint-disable-next-line vue/return-in-computed-property
    currentMode() {
      let color;
      switch (this.selectedMode) {
        case "RGB":
          color = tinycolor(this.pickedColor).toRgb();
          return {
            r: parseInt(color.r),
            g: parseInt(color.g),
            b: parseInt(color.b)
          };
        case "HSL":
          color = tinycolor(this.pickedColor).toHsl();
          return {
            h: parseInt(color.h),
            s: parseInt(color.s * 100),
            l: parseInt(color.l * 100)
          };
        case "HSV":
          color = tinycolor(this.pickedColor).toHsv();
          return {
            h: parseInt(color.h),
            s: parseInt(color.s * 100),
            v: parseInt(color.v * 100)
          };
      }
    },
    hex() {
      return tinycolor(this.pickedColor).toHexString();
    },
    currentTextColor() {
      return tinycolor(this.pickedColor).isLight() ? "#000" : "#fff";
    }
  },
  components: {
    ColorPicker
  },
};
</script>

<style lang="stylus" scoped>
.popup {
  margin: 1em;
  display: inline-flex;
  flex-direction: column;
  box-shadow: 0 3px 6px 0 rgba(0, 0, 0, 0.1), 0 1px 3px 0 rgba(0, 0, 0, 0.08);
  border-radius: 8px;
  overflow: hidden;

  &__title {
    margin: 0.8em 0;
  }

  &__current-rgb {
    padding-bottom: 20px;
  }

  &__current-hex {
    font-size: 18px;
    padding-top: 20px;
  }

  &__save {
    font-size: 15px;
    cursor: pointer;
    padding: 1.2em 1em;
    transition: color 0.2s ease-in;
    border: none;
  }

  &__current-mode {
    display: flex;
    align-items: center;
    justify-items: center;
    margin-bottom 10px;
  }
}

.mode-item {
  display: flex;
  flex-direction: column;
  flex: 1;
}

body {
  background: #f39c12;
}

.select {
  appearance: none;
  outline: 0;
  margin 1em;
  padding: 14px 12px
  background: #fff;
  background-size: 10px;
  transition: border-color 0.1s ease-in-out, box-shadow 0.1s ease-in-out;
  border: 1px solid #ddd;
  border-radius: 3px;
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.2);
  &:hover {
    border: 1px solid rgba(0, 0, 0, .2)
  }
  &:focus {
    border: 1px solid rgba(0, 0, 0, .3)
    box-shadow: 0 2px 3px 0 rgba(0, 0, 0, 0.2);
    outline: none;
  }
}

</style>

