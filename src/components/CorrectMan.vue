<template>
  <div
    style="'z-index': 0"
    v-bind:style="{
      left: x + 'px',
      top: y + 'px',
      'z-index': y,
      position: 'fixed'
    }"
  >
    <div v-if="speaking" class="speech-bubble">NOT ALL MEN</div>
    <div v-if="tweeting" class="tweet-bubble">
      @correctman posted:
      <p>{{ tweet }}</p>
    </div>
    <div v-bind:style="{ opacity: Math.max(this.health, 0) }">
      <div v-if="movingLeft">🏃🏻‍♂️</div>
      <div class="flipped" v-else-if="movingRight">🏃🏻‍♂️</div>
      <div v-else-if="tweeting">
        <div>🧍🏻‍♂️</div>
        <div class="laptop">💻</div>
      </div>
      <div v-else>🧍🏻‍♂️</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "CorrectMan",
  props: {
    womanLocations: Array,
    items: Array
  },
  data: () => ({
    x: window.innerWidth / 2 - 130 / 2,
    y: window.innerHeight / 2 - 130 / 2,
    up: false,
    left: false,
    down: false,
    right: false,
    speaking: false,
    tweeting: false,
    tweet: "",
    health: 1 //float, goes from 1 to -0.1. Goes into the negatives a little to make it feel "fair" to the player.
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
        this.interract();
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
  computed: {
    movingLeft: function() {
      return (
        !this.speaking &&
        !this.tweeting &&
        (this.left || (this.up && !this.right))
      );
    },
    movingRight: function() {
      return (
        !this.speaking &&
        !this.tweeting &&
        (this.right || (this.down && !this.left))
      );
    },
    moving: function() {
      return (
        !this.speaking && (this.up || this.down || this.left || this.right)
      );
    }
  },
  methods: {
    gameLoop() {
      if (!this.speaking && !this.tweeting) {
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
      }
      window.requestAnimationFrame(this.gameLoop);
    },

    interract() {
      if (this.health <= -0.1) {
        return;
      }
      
      index = this.indexOfInteractable();
      if (index != -1 && this.speaking == false) {
        this.tweeting = true;
        this.$emit("tweeting");
        this.health = 1;
        setTimeout(() => (this.tweeting = false), 2000);
        this.generateTweet();
        return;
      }

      var index = this.indexOfWoman();
      if (index != -1 && this.speaking == false) {
        this.speaking = true;
        this.$emit("interrupting", index);
        this.health = 1;
        const sound = new Audio(require("@/assets/manspeak.mp3")).play();
        setTimeout(() => (this.speaking = false), 800);
        return;
      }
    },

    indexOfWoman() {
      var returningIndex = -1;
      this.womanLocations.forEach((womanLoc, index) => {
        if (
          Math.abs(womanLoc.x - this.x) < 100 &&
          this.y - womanLoc.y < 100 &&
          this.y - womanLoc.y > 0
        )
          returningIndex = index;
      });
      return returningIndex;
    },

    indexOfInteractable() {
      var returningIndex = -1;
      this.items.forEach((itemLoc, index) => {
        if (
          Math.abs(itemLoc.x - this.x) < 100 &&
          this.y - itemLoc.y < 100 &&
          this.y - itemLoc.y > 0
        )
          returningIndex = index;
      });
      return returningIndex;
    },

    generateTweet() {
      var tweets = [
        "BUT WHAT ABOUT TOXIC FEMININITY?",
        "FACTS DON'T CARE ABOUT YOUR FEELINGS!"
      ];
      var tweetIndex = Math.floor(Math.random() * tweets.length);
      this.tweet = tweets[tweetIndex];
    },

    reset() {
      this.health = 1;
      this.x = window.innerWidth / 2 - 130 / 2;
      this.y = window.innerHeight / 2 - 130 / 2;
      this.speaking = false;
      this.tweeting = false;
      this.tweet = "";
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
.flipped {
  -moz-transform: scale(-1, 1);
  -webkit-transform: scale(-1, 1);
  -o-transform: scale(-1, 1);
  -ms-transform: scale(-1, 1);
  transform: scale(-1, 1);
}
.tweet-bubble {
  position: relative;
  background: #e5cdc0;
  right: 80px;
  margin-bottom: -155px;
  bottom: 150px;
  width: 280px;
  height: 155px;
  font-family: "Saira Semi Condensed", sans-serif;
  font-size: 0.2em;
  text-align: center;
  vertical-align: center;
  color: #5f5b66;
  line-height: 29px;
  border-radius: 1%;
  padding: 5px 10px 5px 10px;
  box-sizing: border-box;
}
.laptop {
  position: relative;
  font-size: 0.5em;
  bottom: 2em;
  left: 0.25em;
}
</style>
