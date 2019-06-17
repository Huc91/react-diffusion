<template>
  <div>
    <h1>REACTION-DIFFUSION</h1>
    <div>
      <svg
        xmlns="http://www.w3.org/2000/svg"
        :width="width"
        :height="height"
        :viewBox="`0 0 ${width} ${height}`"
      >
        <g>
          <slot></slot>
        </g>
      </svg>
    </div>
  </div>
</template>

<script>
export default {
  name: "Canvas",
  props: {
    width: {
      type: Number,
      default: 164
    },
    height: {
      type: Number,
      default: 164
    }
  }
};
</script>
</style>
