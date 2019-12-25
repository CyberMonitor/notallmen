<template>
  <div
    v-bind:style="{
      left: x + 'px',
      top: y + 'px',
      'z-index': z,
      position: 'fixed'
    }"
  >
    <transition name="fade">
      <div v-if="speaking" class="speech-bubble">{{ storyTyped }}</div>
    </transition>
    {{ emoji }}
  </div>
</template>

<script>
export default {
  name: "Woman",
  props: {
    x: Number,
    y: Number,
    z: Number
  },
  data: () => ({
    emoji: "",
    interrupted: false,
    speaking: false,
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
    this.generateEmoji();
    setTimeout(this.speak, 500);
  },
  methods: {
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

    generateEmoji() {
      var emojiPool = ["🧍🏻‍♀️", "🧍🏼‍♀️", "🧍🏽‍♀️", "🧍🏾‍♀️", "🧍🏿‍♀️"];
      var emojiIndex = Math.floor(Math.random() * emojiPool.length);
      console.log(emojiIndex);
      this.emoji = emojiPool[emojiIndex];
    },

    generateStory() {
      var stories = [
        'Because I had left my drink unattended, I felt like it was my fault. I should have been more careful.',
        'When I told my dad about my rape, he screamed and hit me. It was my fault for wearing that skirt.',
        'First he bettled her. Then he hit her. Finally, he killed her. I wish she hadn\'t stayed.',
        'I was jogging in a sports bra and shorts. A car of teenaged boys pulled up and yelled "run, slut!"'
      ];
      var storyIndex = Math.floor(Math.random() * stories.length);
      this.story = stories[storyIndex];
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
@import url("https://fonts.googleapis.com/css?family=Saira+Semi+Condensed:600&display=swap");

.speech-bubble {
  position: relative;
  background: #e5cdc0;
  right: 80px;
  margin-bottom: -155px;
  bottom: 165px;
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
</style>
