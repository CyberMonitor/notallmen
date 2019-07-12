<template>
  <div id="app">
    <CorrectMan />
      <span v-for="woman in women">
        <Woman
          v-bind:x="woman.x"
          v-bind:y="woman.y"
          v-bind:z="Math.floor(woman.y)"
        />
      </span>
  </div>
</template>

<script>
import CorrectMan from "./components/CorrectMan.vue";
import Woman from "./components/Woman.vue";

export default {
  name: "app",
  components: {
    CorrectMan,
    Woman
  },
  data: () => ({
    women: [],
    max_x: window.innerWidth * 0.8,
    max_y: window.innerHeight * 0.8,
    min_x: 50,
    min_y: 100
  }),
  created() {
    this.interval = setInterval(() => this.createWoman(), 1000);
  },
  methods: {
    createWoman() {
      // do {
      //   var x_pos = Math.random() * (this.max_x - this.min_x) + this.min_x;
      //   var y_pos = Math.random() * (this.max_y - this.min_y) + this.min_y;
      // } while (this.closeToOther(x_pos, y_pos));
      var x_pos = this.max_x / 2 + this.min_x;
      var y_pos = this.max_y / 2 + this.min_y;

      var lady = { x: x_pos, y: y_pos };
      this.women.push(lady);

      if (this.women.length >= 1) {
        clearInterval(this.interval);
      }
    },

    closeToOther(x_pos, y_pos) {
      var tooClose = false;
      for (var i = this.women.length - 1; i >= 0; i--) {
        if (
          Math.abs(this.women[i].x - x_pos) < 100 &&
          Math.abs(this.women[i].y - y_pos) < 100
        ) {
          tooClose = true;
        }
      }
      return tooClose;
    }
  }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Montserrat:400,700");

div {
  font-size: 130px;
}

body {
  background-color: #5f5b66;
  background-size: 100vw 100vh;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
