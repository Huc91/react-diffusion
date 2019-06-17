<template>
  <rect :x="x" :y="y" :width="size" :height="size" :fill="fill"></rect>
</template>

<script>
export default {
  name: "Cell",
  props: {
    x: {
      type: Number,
      default: 0
    },
    y: {
      type: Number,
      default: 0
    },
    fill: {
      type: String,
      default: "#000"
    },
    size: {
      type: Number,
      default: 1
    }
  }
};
</script>
