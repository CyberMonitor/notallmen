<template>
  <div id="app">
    <CorrectMan
      @interrupting="interruptWoman"
      v-bind:womanLocations="womenLocations"
      ref="correctMan"
    />
    <span v-for="woman in womenLocations" v-bind:key="woman.x">
      <Woman
        @speaking="decreaseCorrectMansHealth"
        v-bind:x="woman.x"
        v-bind:y="woman.y"
        v-bind:z="Math.floor(woman.y)"
        ref="women"
      />
    </span>
    <transition name="fade">
      <div v-if="gameEnded" class="game-over">GAME OVER</div>
    </transition>
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
    womenLocations: [],
    max_x: window.innerWidth * 0.8,
    max_y: window.innerHeight * 0.8,
    min_x: 50,
    min_y: 100,
    wave: 0,
    subwave: 0, // each wave has subwaves. subwaves are 10 seconds long.
    // at each subwave, the woman creation interval is decreased, making the
    // wave progressively more difficult
    subwaveLength: 15, //milliseconds
    womanCreationIntervalTime: 2, //milliseconds. halved with each subwave. resets on new wave
    gameEnded: false
  }),
  created() {
    // //milliseconds
    this.wave = 1;
    this.subwave = 1;

    this.womanCreationInterval = setInterval(
      () => this.createWoman(),
      this.womanCreationIntervalTime * 1000 //milliseconds
    );
    this.subwaveInterval = setInterval(
      () => this.startNewSubwave(),
      this.subwaveLength * 1000
    );
  },
  methods: {
    startNewSubwave() {
      this.subwave += 1;
      clearInterval(this.womanCreationInterval);

      if (this.subwave == this.wave + 3) {
        spawnComputer();
      }

      this.womanCreationInterval = setInterval(
        () => this.createWoman(),
        (this.womanCreationIntervalTime * 1000) / (this.subwave / 2)
      );
    },

    spawnComputer() {},

    createWoman() {
      if (this.womenLocations.length >= 5) {
        return;
      }

      do {
        var x_pos = Math.random() * (this.max_x - this.min_x) + this.min_x;
        var y_pos = Math.random() * (this.max_y - this.min_y) + this.min_y;
      } while (this.closeToOther(x_pos, y_pos));

      var lady = { x: x_pos, y: y_pos, interrupted: false };
      this.womenLocations.push(lady);
    },

    closeToOther(x_pos, y_pos) {
      var tooClose = false;
      for (var i = this.womenLocations.length - 1; i >= 0; i--) {
        if (
          Math.abs(this.womenLocations[i].x - x_pos) < 100 &&
          Math.abs(this.womenLocations[i].y - y_pos) < 100
        ) {
          tooClose = true;
        }
      }
      return tooClose;
    },

    interruptWoman(womanIndex) {
      this.$refs.women[womanIndex].interrupted = true;
      setTimeout(() => this.womenLocations.splice(womanIndex, 1), 500);
    },

    decreaseCorrectMansHealth() {
      this.$refs.correctMan.health -= 0.01;
      if (this.$refs.correctMan.health <= 0) {
        this.gameOver();
      }
    },

    gameOver() {
      for (var i = 0; i < this.$refs.women.length; i++) {
        this.$refs.women[i].$destroy();
        //this.womenLocations = [];
      }
      this.$refs.correctMan.$destroy();
      setInterval(() => (this.gameEnded = true), 500);

      clearInterval(this.womanCreationInterval);
      clearInterval(this.subwaveInterval);
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

.game-over {
  position: fixed;
  background: #5f5b66;
  height: 100%;
  width: 100%;
  left: 0;
  top: 0;
  font-family: "Saira Semi Condensed", sans-serif;
  font-size: 1em;
  color: #e5cdc0;
  text-align: center;
  z-index: 2000;
  /*  word-break: break-all;*/
}
</style>
