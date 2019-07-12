<template>
  <div v-bind:style="{ left: x + 'px', top: y + 'px', 'z-index': z, position: 'fixed'}">
    <div v-if="speaking" class="speech-bubble">NOT ALL MEN</div>
    🏃
  </div>
</template>

<script>
export default {
  name: "CorrectMan",
  data: () => ({
    x: window.innerWidth / 2 - 130 / 2,
    y: window.innerHeight / 2 - 130 / 2,
    up: false,
    left: false,
    down: false,
    right: false,
    speaking: false,
  }),
  created() {
    window.addEventListener("keydown", e => {
      if (e.keyCode === 87 /* w */) {
        this.up = true;
      }
      if (e.keyCode === 68 /* d */) {
        this.right = true;
      }
      if (e.keyCode === 83 /* s */) {
        this.down = true;
      }
      if (e.keyCode === 65 /* a */) {
        this.left = true;
      }
      if (e.keyCode === 32 /* a */) {
        this.speaking = !this.speaking;
      }
    });

    window.addEventListener("keyup", e => {
      if (e.keyCode === 87 /* w */) {
        this.up = false;
      }
      if (e.keyCode === 68 /* d */) {
        this.right = false;
      }
      if (e.keyCode === 83 /* s */) {
        this.down = false;
      }
      if (e.keyCode === 65 /* a */) {
        this.left = false;
      }
    });

    window.requestAnimationFrame(this.gameLoop);
  },
  methods: {
    gameLoop() {
      if (this.up) {
        this.y = this.y - 10;
      }
      if (this.right) {
        this.x = this.x + 10;
      }
      if (this.down) {
        this.y = this.y + 10;
      }
      if (this.left) {
        this.x = this.x - 10;
      }
      this.z = this.y
      window.requestAnimationFrame(this.gameLoop);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import url("https://fonts.googleapis.com/css?family=Saira+Semi+Condensed:700&display=swap");

.speech-bubble {
  position: relative;
  background: #e5cdc0;
  right: 80px;
  margin-bottom: -155px;
  bottom: 165px;
  width: 280px;
  height: 155px;
  font-family: "Saira Semi Condensed", sans-serif;
  font-size: 0.5em;
  text-align: center;
  vertical-align: center;
  color: #5f5b66;
  line-height: 75px;
  border-radius: 1%;
}
.speech-bubble:after {
  content: "";
  position: absolute;
  bottom: 0;
  left: 50%;
  width: 0;
  height: 0;
  border: 22px solid transparent;
  border-top-color: #e5cdc0;
  border-bottom: 0;
  margin-left: -22px;
  margin-bottom: -22px;
}

</style>
