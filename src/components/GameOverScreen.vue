<template>
  <div>
    <h2 style="color: #de9c8c">GAME OVER</h2>
    <p v-if="womenCorrected == 1">
      You corrected
      <span style="color: #de9c8c">{{ womenCorrected }}</span> woman.
    </p>
    <p v-else>
      You corrected
      <span style="color: #de9c8c">{{ womenCorrected }}</span> women.
    </p>
    <div style="font-size: 0.6em; font-weight: 500;">
      <p v-if="womenCorrected == 0">
        You're what one might call a 'soy boy'.
      </p>
      <p v-else-if="womenCorrected < 5">
        You sure showed them.
      </p>
      <p v-else-if="womenCorrected < 10">
        Based.
      </p>
      <p v-else-if="womenCorrected < 25">
        Damn. You're like a warrior. Of justice. Wait, is that already a thing?
      </p>
      <p v-else-if="womenCorrected < 50">
        Molyneux would be proud.
      </p>
      <p v-else-if="womenCorrected < 100">
        You're a modern-day Gandhi.
      </p>
      <p v-else>
        Okay, you can stop now.
      </p>
    </div>
    <p>
      Press any key to continue
      <span style="color: #da9295">fighting the good fight.</span>
    </p>
    <div class="credit-box"><a target="_blank" href="https://twitter.com/ctrlshifti">@ctrlshifti</a></div>
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

.credit-box {
  position: fixed;
  bottom: 0.5em;
  left: 0.5em;
  z-index: 1001;
  width: 5em;
  height: 2em;
  font-size: 0.5em;
}

a {
  color: #e5cdc0;
  text-decoration: none;
}
</style>
