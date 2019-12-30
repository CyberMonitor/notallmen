<template>
  <div>
    <h2 style="color: #b1e66c">GAME OVER</h2>
    <p v-if="womenCorrected == 1">
      You corrected
      <span style="color: #f7c33e">{{ womenCorrected }}</span> woman.
    </p>
    <p v-else>
      You corrected
      <span style="color: #f7c33e">{{ womenCorrected }}</span> women.
    </p>
    <p style="font-size: 0.6em" v-if="womenCorrected == 0">
      You're what one might call a 'soy boy'.
    </p>
    <p style="font-size: 0.6em" v-else-if="womenCorrected < 5">
      You sure showed them.
    </p>
    <p style="font-size: 0.6em" v-else-if="womenCorrected < 10">
      Based.
    </p>
    <p style="font-size: 0.6em" v-else-if="womenCorrected < 25">
      Damn. You're like a warrior. Of justice. Wait, is that already a thing?
    </p>
    <p style="font-size: 0.6em" v-else-if="womenCorrected < 50">
      Molyneux would be proud.
    </p>
    <p style="font-size: 0.6em" v-else-if="womenCorrected < 100">
      You're a modern-day Gandhi.
    </p>
    <p style="font-size: 0.6em" v-else>
      Okay, you can stop now.
    </p>
    <p>
      Press any key to continue
      <span style="color: #f26f73">fighting the good fight.</span>
    </p>
  </div>
</template>

<script>
export default {
  name: "GameOverScreen",
  props: {
    womenCorrected: Number
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
