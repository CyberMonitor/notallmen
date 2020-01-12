<template>
  <div v-if="activelyShowingAchievements != []" class="container">
    <transition-group name="list">
      <div
        v-for="achievement in activelyShowingAchievements"
        v-bind:key="achievement.name"
      >
        <div class="achievement-box">
          <div class="emoji">{{ achievement.emoji }}</div>
          <div class="text-box">
            <div class="name">{{ achievement.name }}</div>
            <div class="description">{{ achievement.desc }}</div>
          </div>
        </div>
      </div>
    </transition-group>
  </div>
</template>

<script>
export default {
  name: "Achievements",
  props: {},
  data: () => ({
    achievements: [],
    activelyShowingAchievements: []
  }),
  created() {
    this.achievements.push({
      name: "Woke",
      emoji: "😇",
      desc: "Only corrected white women",
      unlocked: false
    });
    this.achievements.push({
      name: "One of a kind",
      emoji: "🦄",
      desc: "Didn't interrupt any women",
      unlocked: false
    });
    this.achievements.push({
      name: "Machine",
      emoji: "👨🏻‍💻",
      desc: "Corrected a woman before she could speak",
      unlocked: false
    });
    this.achievements.push({
      name: "SJWs Wrecked",
      emoji: "🥀",
      desc: "Corrected 10 women",
      unlocked: false
    });
    this.achievements.push({
      name: "Alpha",
      emoji: "🏋️‍♂️",
      desc: "Corrected 25 women",
      unlocked: false
    });
    this.achievements.push({
      name: "Red pilled",
      emoji: "🖍️",
      desc: "Corrected 50 women",
      unlocked: false
    });
    this.achievements.push({
      name: "Free speech warrior",
      emoji: "🗽",
      desc: "Corrected 100 women",
      unlocked: false
    });
  },
  computed: {},
  methods: {
    unlockAchievement(achievementName) {
      var achievementToUnlock = this.achievements.find(
        achievement => achievement.name == achievementName
      );
      if (!achievementToUnlock.unlocked) {
        achievementToUnlock.unlocked = true;
        this.activelyShowingAchievements.push(achievementToUnlock);
        setTimeout(
          () => this.activelyShowingAchievements.splice(achievementToUnlock, 1),
          3000
        );
      }
    }
  }
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css?family=Saira+Semi+Condensed:600,500&display=swap");

.achievement-box {
  background: #e5cdc0;
  width: 20em;
  height: 6em;
  font-family: "Saira Semi Condensed", sans-serif;
  font-size: 1em;
  color: #5f5b66;
  line-height: 18px;
  border-radius: 1%;
  padding: 5px 10px 5px 10px;
  box-sizing: border-box;
  white-space: pre-line;
  margin-top: 0.4em;
}
.container {
  display: flex; /* or inline-flex */
  flex-direction: column-reverse;
  justify-content: space-between;
  position: fixed;
  bottom: 0.5em;
  right: 0.5em;
  z-index: 1002;
}

.emoji {
  font-size: 3em;
  padding-top: 0.8em;
  float: left;
  width: 10%;
}
.name {
  padding-top: 0.2em;
  font-size: 1.5em;
  font-weight: 600;
  margin-bottom: 0.5em;
}
.description {
  font-weight: 500;
  text-align: center;
  padding-left: 3em;
  padding-right: 3em;
}
.text-box {
  text-align: center;
  float: left;
  width: 90%;
  padding-top: 0.4em;
}

.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active,
.list-leave-active {
  transition: all 1s;
}
.list-enter, .list-leave-to /* .list-leave-active below version 2.1.8 */ {
  opacity: 0;
  transform: translateY(30px);
}
</style>
