<template>
  <div class="achievement-box container">
    <div class="emoji">😇</div>
    <div style="text-align: center;">
      <div class="title">Woke</div>
      <div class="description">Only corrected white women</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Achievements",
  props: {},
  data: () => ({
    emojiList: []
  }),
  created() {},
  computed: {},
  methods: {}
};
</script>

<style scoped>
.achievement-box {
  position: fixed;
  bottom: 0.5em;
  right: 0.5em;
  background: #e5cdc0;
  width: 20em;
  height: 5em;
  font-family: "Saira Semi Condensed", sans-serif;
  font-size: 1em;
  color: #5f5b66;
  line-height: 29px;
  border-radius: 1%;
  padding: 5px 10px 5px 10px;
  box-sizing: border-box;
  white-space: pre-line;
  z-index: 1001;
}
.container {
  display: flex; /* or inline-flex */
  flex-direction: flex-start;
  align-items: center;
}
.emoji {
  font-size: 3em;
}
.title {
  font-size: 1.5em;
}
.description {
  padding-left: 10px;
  padding-right: 10px;
}
</style>
