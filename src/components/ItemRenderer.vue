<template>
  <div
    v-bind:style="{
      left: x + 'px',
      top: y + 'px',
      'z-index': z,
      position: 'fixed'
    }"
  >
    <div class="item"><p>{{ emoji }}</p></div>
  </div>
</template>

<script>
export default {
  name: "ItemRenderer",
  props: {
    x: Number,
    y: Number,
    z: Number,
    emoji: String
  },
  data: () => ({}),
  created() {},
  methods: {}
};
</script>

<style scoped>
.item {
  font-size: 0.5em;
  position: relative;
  top: 0.5em;
}
</style>
