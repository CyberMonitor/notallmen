<template>
  <div id="app">
    <div v-if="gameBegun" class="main-game">
      <CorrectMan
        @interrupting="interruptWoman"
        @tweeting="tweet"
        v-bind:womanLocations="womenLocations"
        v-bind:items="items"
        ref="correctMan"
      />
      <span v-for="woman in womenLocations" v-bind:key="woman.x">
        <Woman
          @speaking="decreaseCorrectMansHealth"
          v-bind:x="woman.x"
          v-bind:y="woman.y"
          v-bind:z="Math.floor(woman.y)"
          v-bind:numWomen="womenLocations.length"
          v-bind:indexInWomen="womenLocations.indexOf(woman)"
          ref="women"
        />
      </span>
      <span v-for="item in items" v-bind:key="item.x">
        <ItemRenderer
          v-bind:x="item.x"
          v-bind:y="item.y"
          v-bind:z="Math.floor(item.y)"
          v-bind:emoji="item.emoji"
        />
      </span>
    </div>
    <transition name="fade">
      <div v-if="gameEnded" class="info-screen">
        <GameOverScreen v-bind:womenCorrected="womenCorrected" />
      </div>
    </transition>
    <transition name="fade">
      <div v-if="!gameBegun" class="info-screen">
        <h2 style="color: #b1e66c;">Not All Men</h2>
        <p><span style="color: #f7c33e">WASD</span> - Move</p>
        <p><span style="color: #f7c33e">Spacebar</span> - Correct</p>
        <p>
          Correct women to <span style="color: #f26f73">stay relevant!</span>
        </p>

        <div style="position: relative">
          <p
            style="position: fixed; bottom: 1em; width:100%; text-align: center; font-size: 0.6em"
          >
            TW: Contains descriptions of sexual abuse and violence.
          </p>
        </div>
      </div>
    </transition>
    <Achievements/>
  </div>
</template>

<script>
import CorrectMan from "./components/CorrectMan.vue";
import Woman from "./components/Woman.vue";
import ItemRenderer from "./components/ItemRenderer.vue";
import GameOverScreen from "./components/GameOverScreen.vue";
import Achievements from "./components/Achievements.vue";

export default {
  name: "app",
  components: {
    CorrectMan,
    Woman,
    ItemRenderer,
    GameOverScreen,
    Achievements
  },
  data: () => ({
    gameBegun: false,
    womenLocations: [],
    womenCorrected: 0,
    items: [],
    max_x: window.innerWidth - 250,
    max_y: window.innerHeight - 200,
    min_x: 100,
    min_y: 200,
    wave: 0,
    subwave: 0, // each wave has subwaves. subwaves are 10 seconds long.
    // at each subwave, the woman creation interval is decreased, making the
    // wave progressively more difficult
    subwaveLength: 10, //milliseconds
    womanCreationIntervalTime: 2, //milliseconds. halved with each subwave. resets on new wave
    gameEnded: false
  }),
  created() {
    window.addEventListener("click", this.startGame);
    window.addEventListener("keydown", this.startGame);
    window.addEventListener("resize", this.resizeGamescreen);
  },
  methods: {
    resizeGamescreen() {
      this.max_x = window.innerWidth - 250;
      this.max_y = window.innerHeight - 200;
    },

    startGame() {
      this.gameBegun = true;
      this.gameEnded = false;
      window.removeEventListener("click", this.startGame);
      window.removeEventListener("keydown", this.startGame);
      this.startNewWave();
    },

    resetGame() {
      this.womenCorrected = 0;
      this.wave = 0;
      this.subwave = 0;
      this.womenLocations = [];
      this.items = [];
      this.$refs.correctMan.reset();
      window.removeEventListener("click", this.resetGame);
      window.removeEventListener("keydown", this.resetGame);
      this.gameBegun = true;
      this.gameEnded = false;
      this.startNewWave();
    },

    startNewSubwave() {
      this.subwave += 1;
      clearInterval(this.womanCreationInterval);

      if (this.subwave == 3 + this.wave - 1) {
        this.spawnComputer();
      }

      this.womanCreationInterval = setInterval(
        () => this.createWoman(),
        (this.womanCreationIntervalTime * 1000) / (this.subwave - 1)
      );
    },

    startNewWave() {
      this.wave += 1;
      clearInterval(this.womanCreationInterval);
      clearInterval(this.subwaveInterval);

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

    spawnComputer() {
      var x_pos = Math.random() * (this.max_x - this.min_x) + this.min_x;
      var y_pos = Math.random() * (this.max_y - this.min_y) + this.min_y;
      var computer = { x: x_pos, y: y_pos, emoji: "💻" };
      this.items.push(computer);
      new Audio(require("@/assets/computerspawn.mp3")).play();
    },

    createWoman() {
      if (this.womenLocations.length >= 1 + this.wave) {
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
      this.$refs.women[womanIndex].destroy();
      setTimeout(() => this.womenLocations.splice(womanIndex, 1), 500);
      this.womenCorrected += 1;
    },

    tweet() {
      this.items = [];
      for (var i = 0; i < this.$refs.women.length; i++) {
        this.$refs.women[i].destroy();
        this.womenCorrected += 1;
      }
      setTimeout(() => (this.womenLocations = []), 500);
      new Audio(require("@/assets/mantweeting.mp3")).play();
      this.startNewWave();
    },

    decreaseCorrectMansHealth() {
      this.$refs.correctMan.health -= 0.01;
      if (this.$refs.correctMan.health <= -0.1) {
        this.gameOver();
      }
    },

    gameOver() {
      for (var i = 0; i < this.$refs.women.length; i++) {
        this.$refs.women[i].destroy();
      }
      clearInterval(this.womanCreationInterval);
      clearInterval(this.subwaveInterval);

      setTimeout(() => {
        this.gameEnded = true;
      }, 500);
      setTimeout(() => {
        window.addEventListener("click", this.resetGame);
        window.addEventListener("keydown", this.resetGame);
      }, 1500);
    }
  }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css?family=Montserrat:400,700");

.main-game {
  font-size: 8em;
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

.info-screen {
  position: fixed;
  background: #5f5b66;
  height: 100%;
  width: 100%;
  left: 0;
  top: 0;
  font-family: "Saira Semi Condensed", sans-serif;
  color: #e5cdc0;
  text-align: center;
  z-index: 1000;
  font-size: 2.5em;
  /*  word-break: break-all;*/
}

h2 {
  margin-bottom: 0.5em;
  margin-top: 0.25em;
}

p {
  margin-top: 0.1em;
  margin-bottom: 0.1em;
}

p:last-child {
  margin-top: 1em;
}
</style>
