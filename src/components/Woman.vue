<template>
  <div
    v-bind:style="{
      left: x + 'px',
      top: y + 'px',
      'z-index': Math.floor(y),
      position: 'fixed'
    }"
  >
    <transition name="fade">
      <div v-if="speaking" class="speech-bubble">{{ storyTyped }}</div>
    </transition>
    <transition name="fade">
      <div v-if="showImage"><img :src="imgUrl(image)" /></div>
    </transition>
  </div>
</template>

<script>
export default {
  name: "Woman",
  props: {
    x: Number,
    y: Number,
    numWomen: Number,
    indexInWomen: Number,
    wave: Number
  },
  watch: {
    numWomen: function(newVal, oldVal) {
      if (newVal <= 4) {
        this.playingSound = true;
      } else this.playingSound = false;
    }
  },
  data: () => ({
    image: "",
    numImages: 12,
    interrupted: false,
    speaking: false,
    showImage: false,
    story: "",
    storyWords: "",
    currentWord: 0,
    storyTyped: "",
    typewiterPosition: 0,
    charsOnCurrentLine: 0,
    maxCharsOnLine: 23
  }),
  created() {
    this.generateStory();
    this.generateImage();
    setTimeout(() => (this.showImage = true), 100);
    setTimeout(this.speak, 700);
  },
  methods: {
    imgUrl(pic) {
      return require("@/assets/images/woman" + pic + ".png");
    },
    speak() {
      this.speaking = true;
      this.storyWords = this.story.split(" ");
      this.typeWriter();
    },
    typeWriter() {
      if (this.typewiterPosition < this.story.length && !this.interrupted) {
        var charToWrite = this.story.charAt(this.typewiterPosition);
        this.storyTyped += charToWrite;
        this.typewiterPosition++;
        this.charsOnCurrentLine++;

        if (
          !charToWrite.match(/^[.,:!? ]/) &&
          this.indexInWomen >= 0 &&
          this.indexInWomen <= 4
        ) {
          const sound = new Audio(require("@/assets/womanspeak1.mp3"));
          sound.volume = Math.max(1 - this.numWomen / 6, 0.25);
          sound.play();
        }

        if (charToWrite == " ") {
          this.currentWord++;
          if (
            this.charsOnCurrentLine +
              this.storyWords[this.currentWord].length >=
            this.maxCharsOnLine
          ) {
            this.storyTyped += "\n";
            this.charsOnCurrentLine = 0;
          }
        }
        this.$emit("speaking");
        setTimeout(this.typeWriter, 70);
      }
    },

    generateImage() {
      var imageNum = Math.floor(Math.random() * this.numImages + 1);
      this.image = imageNum;
    },

    generateStory() {
      var stories = [
        "Because I had left my drink unattended, I felt like it was my fault. I should have been more careful.",
        "When I told my dad about what happened, he hit me. It was my fault for wearing that skirt.",
        "First he belittled her. Then he hit her. Finally, he killed her. I wish she hadn't stayed.",
        'I was jogging in a sports bra and shorts. A car of teenaged boys pulled up and yelled "run, slut!"',
        "I was at a work Christmas party. The CEO kept staring at my cleavage while we talked.",
        "I was 16, on the bus. An older man sat beside me and grabbed my breasts. I was too afraid to speak up.",
        "During an argument, my husband pushed me down a staircase and broke my arm. I stayed for the kids.",
        'My girlfriend and I were walking down the street when a man driving by yelled "dykes!" at us.',
        "When I told my parents about what my uncle did, they told me to stay silent. That I would hurt the family.",
        "I was at a bar. He forced me onto his lap and kissed me. All his friends laughed."
      ];
      var storyIndex = Math.floor(Math.random() * stories.length);
      this.story = stories[storyIndex];
    },

    destroy() {
      this.speaking = false;
      this.interrupted = true;
      setTimeout(() => (this.showImage = false), 300);
      setTimeout(() => this.$destroy(), 500);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import url("https://fonts.googleapis.com/css?family=Saira+Semi+Condensed:600&display=swap");

.speech-bubble {
  position: relative;
  background: #e5cdc0;
  right: 90px;
  margin-bottom: -155px;
  bottom: 175px;
  width: 280px;
  height: 155px;
  font-family: "Saira Semi Condensed", sans-serif;
  font-size: 0.2em;
  font-weight: 600;
  text-align: center;
  vertical-align: center;
  color: #5f5b66;
  line-height: 29px;
  border-radius: 1%;
  padding: 5px 10px 5px 10px;
  box-sizing: border-box;
  white-space: pre-line;
  /*  word-break: break-all;*/
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
img {
  image-rendering: pixelated;
  image-rendering: -moz-crisp-edges;
  image-rendering: crisp-edges;
}
</style>
