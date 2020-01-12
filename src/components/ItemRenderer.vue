<template>
  <div
    v-bind:style="{
      left: x + 'px',
      top: y + 'px',
      'z-index': z,
      position: 'fixed'
    }"
  >
    <div>
      <p><img :src="imgUrl(image)" /></p>
    </div>
  </div>
</template>

<script>
export default {
  name: "ItemRenderer",
  props: {
    x: Number,
    y: Number,
    z: Number,
    image: String
  },
  data: () => ({}),
  created() {},
  methods: {
    imgUrl(pic) {
      return require("@/assets/images/" + pic);
    }
  }
};
</script>

<style scoped>
.item {
  position: relative;
  top: 0.5em;
}
img {
  image-rendering: pixelated;
  image-rendering: -moz-crisp-edges;
  image-rendering: crisp-edges;
  /* max-height: 1em; */
}
</style>
