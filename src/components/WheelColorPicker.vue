<template>
  <div
    @mousemove="onMouseMove"
    @mousedown="onMouseDown"
    @mouseup="onMouseUp"
    class="color-wheel"
    ref="colorWheel"
    :style="{width: `${size}px`, heigth: `${size}px`}">
      <canvas class="color-wheel__gradient" ref="colorWheelGradient" :width="size" :height="size"/>
      <div class="color-wheel__pointer" ref="pointer" :style="{ transform: pointerPosition }">
    </div>
  </div>
</template>

<script>
import tinycolor from 'tinycolor2'

export default {
  name: "ColorWheel",
  props: {
    size: {
      type: Number,
      default: 300
    },
    value: {
      type: String
    },
  },
  data() {
    return {
      canvas: null,
      ctx: null,
      currentColor: null,
      pointerPosition: null,
      mousePressed: false
    };
  },
  mounted() {
    this.canvas = this.$refs.colorWheelGradient;
    this.ctx = this.canvas.getContext("2d");
    this.drawCircle();
    if (this.value) this.setPositionByColor(this.value);
  },

  watch: {
    value(color) {
      if (this.currentColor !== color) {
        this.setPositionByColor(color)
      }
    }
  },
  methods: {
    onMouseMove(e) {
      if (!this.mousePressed) return;
      this.getColor(e);
      this.getPos(e);
    },

    onMouseDown(e) {
      this.mousePressed = true;
      this.getColor(e);
      this.getPos(e);
    },

    onMouseUp() {
      this.mousePressed = false;
    },

    drawCircle() {
      const CX = this.canvas.width / 2,
        CY = this.canvas.height / 2,
        sx = CX,
        sy = CY;

      for (let i = 0; i < 360; i += 0.1) {
        const rad = (i * Math.PI) / 180 - Math.PI / 2;
        const RX = CX + sx * Math.cos(rad);
        const RY = CY + sy * Math.sin(rad);

        const grad = this.ctx.createLinearGradient(CX, CY, RX, RY);
        grad.addColorStop(0, "white");
        grad.addColorStop(1, `hsla(${i}, 100%, 50%, 1.0)`);
        this.ctx.strokeStyle = grad;
        this.ctx.beginPath();
        this.ctx.moveTo(CX, CY);
        this.ctx.lineTo(RX, RY);
        this.ctx.closePath();
        this.ctx.stroke();
        this.ctx.save();
      }
    },

    setPositionByColor(color) {
      const { h, l } = tinycolor(color).toHsl()
      const center = {
        x: this.canvas.width / 2,
        y: this.canvas.height / 2
      };
      const dot = {
        x: this.canvas.width / 2,
        y: (((100 - l*100) / 50) * this.canvas.width) / 2,
      };
      const angle = (h * Math.PI) / 180;
      const newCoors = rotate(angle, center, dot);
      this.pointerPosition = `translate(${newCoors.x - 7}px, ${newCoors.y - 7}px)`;
    },

    getColor(e) {
      const x = e.pageX - this.$refs.colorWheel.offsetLeft;
      const y = e.pageY - this.$refs.colorWheel.offsetTop;
      const [r, g, b] = this.ctx.getImageData(x, y, 1, 1).data;
      const pixelColor = `rgb(${r},${g},${b})`;
      this.currentColor = pixelColor;
      this.$emit("input", pixelColor);
    },

    getPos(e) {
      const x = e.pageX - this.$refs.colorWheel.offsetLeft;
      const y = e.pageY - this.$refs.colorWheel.offsetTop;
      this.pointerPosition = `translate(${x - 7}px, ${y - 7}px)`;
    }
  }
};

function rotate(angle, origin, coords) {
  angle = Number(angle);
  const x =
    (coords.x - origin.x) * Math.cos(angle) -
    (coords.y - origin.y) * Math.sin(angle);
  const y =
    (coords.x - origin.x) * Math.sin(angle) +
    (coords.y - origin.y) * Math.cos(angle);
  return { x: parseInt(x + origin.x * 1), y: parseInt(y + origin.y * 1) };
}

</script>

<style lang="stylus" scoped>
.color-wheel {
  cursor: pointer;
  position: relative;
  border-radius: 50%;
  overflow: hidden;

  &__pointer {
    top: 0;
    left: 0;
    position: absolute;
    width: 14px;
    height: 14px;
    border: 2px solid rgba(0, 0, 0, 0.5);
    box-sizing: border-box;
    border-radius: 50%;
  }

  &__picked {
    white-space: nowrap;
    padding: 2px;
    font-size: 10px;
    position: absolute;
    left: calc(100% + 8px);
    top: -4px;
    border-radius: 2px;
    width: auto;
    height: 14px;
    background: rgba(255, 255, 255, 0.8);
  }

  &__gradient {
    width: 100%;
    height: 100%;
    border-radius: 50%;
  }
}
</style>
