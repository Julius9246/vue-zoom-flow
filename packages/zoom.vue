<!--
 * User: Julius9246
 * Date: 2022/5/20
 * Time: 13:14
-->
<template>
  <div
    class="super-flow-container__zoom"
    onselectstart="return false"
  >
    <div
      class="super-flow-container__zoom-item"
      @click.prevent.stop="handleZoom(1)"
    ><span>+</span></div>
    <div
      class="super-flow-container__zoom-item"
      @click.prevent.stop="handleZoom(0)"
    ><span>-</span></div>
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: String,
      default: ""
    },
    /**
     * @description 缩小极限值, 默认0.4
     */
    lessenLimit: {
      type: Number,
      default: 0.4,
      validator: function(value) {
        return value > 0;
      }
    },
    /**
     * @description 放大极限值，默认2
     */
    magnifyLimit: {
      type: Number,
      default: 2
    }
  },
  data() {
    return {};
  },
  computed: {
    transformVal: {
      get() {
        return this.value;
      },
      set(str) {
        return this.$emit("update:value", str);
      }
    }
  },
  methods: {
    handleZoom(type) {
      const step = type ? 0.2 : -0.2;
      this.setChartScale(step);
    },
    setChartScale(step) {
      let matrix = "";
      let targetScale = 1;
      if (!this.transformVal) {
        this.transformVal =
          "matrix(" + (1 + step) + ", 0, 0, " + (1 + step) + ", 0, 0)";
      } else {
        matrix = this.transformVal.split(",");
        if (this.transformVal.indexOf("3d") === -1) {
          targetScale = parseFloat(
            Math.abs(parseFloat(matrix[3]) + step).toFixed(1)
          );

          if (
            targetScale >= this.lessenLimit &&
            targetScale <= this.magnifyLimit
          ) {
            matrix[0] = "matrix(" + targetScale.toFixed(1);
            matrix[3] = targetScale.toFixed(1);
            this.transformVal = matrix.join(",");
          }
        } else {
          targetScale = Math.abs(parseFloat(matrix[5]) + step);
          if (
            targetScale >= this.lessenLimit &&
            targetScale <= this.magnifyLimit
          ) {
            matrix[0] = "matrix3d(" + targetScale.toFixed(1);
            matrix[5] = targetScale.toFixed(1);
            this.transformVal = matrix.join(",");
          }
        }
      }
    }
  }
};
</script>

<style lang="less" scoped>
.super-flow-container__zoom {
  position: absolute;
  top: 10px;
  right: 10px;
  z-index: 1000;

  &-item {
    width: 38px;
    height: 30px;
    color: #606266;
    font-size: 26px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #ffffff;
    border: 1px solid #d3d5d9;
    border-radius: 4px;
    &:first-child {
      border-radius: 4px 4px 0 0;
    }
    &:last-child {
      border-radius: 0 0 4px 4px;
      border-top: 0;
    }
    &:hover {
      background: #f5f7fa;
    }
  }
}
</style>