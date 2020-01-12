<template>
  <div id="app">
    <div v-if="gameBegun" class="main-game">
      <CorrectMan
        @interrupting="interruptWoman"
        @yelling="yell"
        v-bind:womanLocations="womenLocations"
        v-bind:items="items"
        ref="correctMan"
      />
      <span v-for="woman in womenLocations" v-bind:key="woman.x">
        <Woman
          @speaking="decreaseCorrectMansHealth"
          v-bind:x="woman.x"
          v-bind:y="woman.y"
          v-bind:numWomen="womenLocations.length"
          v-bind:indexInWomen="womenLocations.indexOf(woman)"
          v-bind:wave="wave"
          ref="women"
        />
      </span>
      <span v-for="item in items" v-bind:key="item.x">
        <ItemRenderer
          v-bind:x="item.x"
          v-bind:y="item.y"
          v-bind:z="Math.floor(item.y)"
          v-bind:image="item.image"
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
        <h2 style="color: #da9295;">Not All Men</h2>
        <p><span style="color: #de9c8c">WASD</span> - Move</p>
        <p><span style="color: #de9c8c">Spacebar</span> - Correct</p>
        <p>
          Correct women to <span style="color: #da9295">stay relevant!</span>
        </p>

        <div style="position: relative">
          <p
            style="font-weight: 500; position: fixed; bottom: 1em; width:100%; text-align: center; font-size: 0.6em"
          >
            TW: Contains descriptions of sexual abuse and violence.
          </p>
        </div>
      </div>
    </transition>
    <Achievements ref="achievements" />
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
    max_y: window.innerHeight - 250,
    min_x: 100,
    min_y: 200,
    wave: 0,
    subwave: 0, // each wave has subwaves. subwaves are 10 seconds long.
    // at each subwave, the woman creation interval is decreased, making the
    // wave progressively more difficult
    subwaveLength: 10, //milliseconds
    womanCreationIntervalTime: 2, //milliseconds. halved with each subwave. resets on new wave
    gameEnded: false,
    wokeAchievementPossible: true
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
      window.removeEventListener("keydown", this.resetGame);
      this.gameBegun = true;
      this.gameEnded = false;
      this.startNewWave();
    },

    startNewSubwave() {
      this.subwave += 1;
      clearInterval(this.womanCreationInterval);

      if (this.subwave == 3 + this.wave - 1) {
        this.spawnMegaphone();
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

    spawnMegaphone() {
      var x_pos = Math.random() * (this.max_x - this.min_x) + this.min_x;
      var y_pos = Math.random() * (this.max_y - this.min_y) + this.min_y;
      var megaphone = { x: x_pos, y: y_pos, image: "megaphone.png" };
      this.items.push(megaphone);
      new Audio(require("@/assets/megaphonespawn.mp3")).play();
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
      if (
        this.$refs.women[womanIndex].image == "1" ||
        this.$refs.women[womanIndex].image == "4" ||
        this.$refs.women[womanIndex].image == "5" ||
        this.$refs.women[womanIndex].image == "6"||
        this.$refs.women[womanIndex].image == "8"||
        this.$refs.women[womanIndex].image == "10"||
        this.$refs.women[womanIndex].image == "11"
      ) {
        this.wokeAchievementPossible = false;
      }
      if (this.$refs.women[womanIndex].typewiterPosition == 0) {
        this.$refs.achievements.unlockAchievement("Machine");
      }

      this.$refs.women[womanIndex].destroy();
      setTimeout(() => this.womenLocations.splice(womanIndex, 1), 500);
      this.womenCorrected += 1;

      if (this.womenCorrected == 100) {
        this.$refs.achievements.unlockAchievement("Free speech warrior");
      } else if (this.womenCorrected == 50) {
        this.$refs.achievements.unlockAchievement("Red pilled");
      } else if (this.womenCorrected == 25) {
        this.$refs.achievements.unlockAchievement("Alpha");
      } else if (this.womenCorrected == 10) {
        this.$refs.achievements.unlockAchievement("SJWs Wrecked");
      }
    },

    yell() {
      this.items = [];
      var totalWomen = this.$refs.women.length;
      for (var i = 0; i < totalWomen; i++) {
        this.interruptWoman(i);
      }
      setTimeout(() => this.womenLocations = [], 500);
      new Audio(require("@/assets/manyelling.mp3")).play();
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
        window.addEventListener("keydown", this.resetGame);
      }, 1500);

      if (this.wokeAchievementPossible && this.womenCorrected >= 1) {
        this.$refs.achievements.unlockAchievement("Woke");
      }
      if (this.womenCorrected == 0) {
        this.$refs.achievements.unlockAchievement("One of a kind");
      }
    }
  }
};
</script>

<style>
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
  font-weight: 600;
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
