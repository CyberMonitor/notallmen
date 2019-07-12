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
      <div v-if="speaking" class="speech-bubble">{{ messageTyped }}</div>
    </transition>
    💃
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
    speaking: false,
    message:
      'Because I was considered a "party girl" I felt I deserved it. It was my fault for being there.',
    messageWords: "",
    currentWord: 0,
    messageTyped: "",
    typewiterPosition: 0,
    charsOnCurrentLine: 0,
    maxCharsOnLine: 24
  }),
  created() {
    setTimeout(this.speak, 1000);
  },
  methods: {
    speak() {
      this.speaking = true;
      this.messageWords = this.message.split(" ");
      this.typeWriter();
    },
    typeWriter() {
      if (this.typewiterPosition < this.message.length) {
        var charToWrite = this.message.charAt(this.typewiterPosition);
        this.messageTyped += charToWrite;
        this.typewiterPosition++;
        this.charsOnCurrentLine++;

        if (charToWrite == " ") {
          this.currentWord++;
          if (
            this.charsOnCurrentLine +
              this.messageWords[this.currentWord].length >=
            this.maxCharsOnLine
          ) {
            this.messageTyped += "\n";
            this.charsOnCurrentLine = 0;
          }
        }
        setTimeout(this.typeWriter, 70);
      }
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
