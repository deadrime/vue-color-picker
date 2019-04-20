<template>
  <div class="color-picker" :style="{width: `${width}px`, height: `${height}px`}">
    <div
      class="color-picker__spectrum"
      :style="{background: spectrumBg, width: `${width}px`, height: `${height}px`}"
      ref="spectrum"
      @mousedown="onSpectrumMouseDown"
      @mouseup="onSpectrumMouseUp"
    >
      <div
        class="current-lightness"
        :style="{
          transform: lightnessTransform,
          background: currentColor,
          width: `${cursorSize}px`,
          height: `${cursorSize}px`,
        }"
      ></div>
    </div>
    <div
      class="slider"
      @click="pickHue"
      ref="slider"
      @mousedown="onSliderMouseDown"
      @mouseup="onSliderMouseUp"
    >
      <div class="slider__bg"/>
      <div class="slider__current" :style="{transform:sliderTransform, background: spectrumBg}"/>
    </div>
  </div>
</template>

<script>
import tinycolor from "tinycolor2";

export default {
  name: "Color-picker",
  props: {
    width: {
      type: Number,
      default: 300
    },
    height: {
      type: Number,
      default: 300
    },
    value: {
      type: String
    },
    cursorSize: {
      type: Number,
      default: 20,
    }
  },
  data() {
    return {
      h: 0,
      s: 100,
      l: 50,
      sliderPos: 0,
      lightnessPos: {
        x: this.width|| 0,
        y: 0,
      },
      mousePressed: false
    };
  },
  computed: {
    spectrumBg() {
      return tinycolor({ h: this.h, s: 100, l: 50 }).toRgbString();
    },
    currentColor() {
      return tinycolor({ h: this.h, s: this.s, l: this.l }).toRgbString();
    },
    sliderTransform() {
      return `translateX(${this.sliderPos - 7}px)`;
    },
    lightnessTransform() {
      return `translate(${this.lightnessPos.x - this.cursorSize/2}px, ${this.lightnessPos.y - this.cursorSize/2}px)`;
    }
  },
  watch: {
    currentColor() {
      this.$emit('input', this.currentColor)
    }
  },
  methods: {
    onSpectrumMouseMove(e) {
      if (!this.mousePressed) return;
      this.pickLightness(e);
    },
    clearListeners() {
      window.removeEventListener("mousemove", this.onSliderMouseMove);
      window.removeEventListener("mousemove", this.onSpectrumMouseMove);
      window.removeEventListener("mouseup", this.clearListeners);
    },
    onSpectrumMouseDown(e) {
      e.preventDefault();
      window.addEventListener("mousemove", this.onSpectrumMouseMove);
      window.addEventListener("mouseup", this.clearListeners)
      this.mousePressed = true;
      this.pickLightness(e);
    },
    onSpectrumMouseUp() {
      this.mousePressed = false;
    },
    onSliderMouseMove(e) {
      if (!this.mousePressed) return;
      this.pickHue(e);
    },
    onSliderMouseDown(e) {
      e.preventDefault();
      window.addEventListener("mousemove", this.onSliderMouseMove);
      window.addEventListener("mouseup", this.clearListeners)
      this.mousePressed = true;
      this.pickHue(e);
    },
    onSliderMouseUp() {
      this.mousePressed = false;
    },
    pickHue(e) {
      const { left, width } = this.$refs.slider.getBoundingClientRect();
      const h = ((e.pageX - left) / width) * 360;
      this.h = h;
      let slidetPos = e.pageX - left;
      if (slidetPos > width) slidetPos = width;
      if (slidetPos < 0) slidetPos = 0;
      this.sliderPos = slidetPos;
    },
    pickLightness(e) {
      const spectrum = this.$refs.spectrum;
      const { left, top, width, height } = spectrum.getBoundingClientRect();
      let x = e.pageX - left;
      if (x > width) x = width;
      if (x < 0) x = 0;
      let y = e.pageY - window.pageYOffset - top;
      if (y > height) y = height;
      if (y < 0) y = 0.00001
      const hsvValue = 1 - y / height;
      const hsvSaturation = x / width;
      this.l = (hsvValue / 2) * (2 - hsvSaturation);
      this.s = (hsvValue * hsvSaturation) / (1 - Math.abs(2 * this.l - 1))

      this.lightnessPos = {
        x,
        y,
      };
    }
  }
};
</script>


<style lang="stylus" scoped>

.current-lightness {
  z-index: 1;
  cursor: pointer;
  position: absolute;
  box-sizing: border-box;
  border: 2px white solid;
  border-radius: 50%;
}

.color-picker {
  display flex
  flex-direction column

  &__spectrum {
    position: relative;
    border-top-left-radius: 10px;
    border-top-right-radius: 10px;

    &:before, &:after {
      content: '';
      position: absolute;
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
    }

    &:before {
      background: linear-gradient(to right, rgb(255, 255, 255), rgba(255, 255, 255, 0));
    }

    &:after {
      background: linear-gradient(to top, rgb(0, 0, 0), rgba(0, 0, 0, 0));
    }
  }
}

.slider {
  height: 14px;
  width: 100%;
  position: relative;

  &__current {
    z-index 2
    cursor pointer;
    box-sizing: border-box;
    border: 2px solid #fff;
    border-bottom none
    position: absolute;
    left: 0;
    top: -10px;
    width: 14px;
    height: 24px;
    background: #000;
  }

  &__bg {
    height: 100%;
    width: 100%;
    background: linear-gradient(to right, hsl(0, 100%, 50%), hsl(60, 100%, 50%), hsl(120, 100%, 50%), hsl(180, 100%, 50%), hsl(240, 100%, 50%), hsl(300, 100%, 50%), hsl(360, 100%, 50%));
  }
}
</style>
