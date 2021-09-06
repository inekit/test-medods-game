<template>
  <div id="app">
    <label>Раунд {{ round }}</label>
    <button @click="playRound()">Start</button>
    <select v-model="dLevel"
      ><option v-for="i in Object.keys(timings)" :key="i">{{
        i
      }}</option></select
    >
    <game-area @stopRound="onStopRound" />
  </div>
</template>

<script>
import GameArea from "./components/GameArea.vue";

export default {
  name: "App",
  data() {
    return {
      round: 1,
      dLevel: "easy",
      timings: {
        easy: 1.5,
        medium: 1.0,
        hard: 0.4,
      },
    };
  },
  components: {
    GameArea,
  },
  methods: {
    // Озвучиваем и сохраняем результаты раунда
    onStopRound(isWon) {
      if (!isWon) {
        alert("Игра накончена, вы продвинулись до " + this.round + " раунда!");
        this.round = 1;
      } else {
        alert(this.round + "раунд!");
        this.round += 1;
      }
    },

    // Придумываем последовательность нажатия кнопок
    playRound() {
      var sequence = [];
      for (var i = 0; i < this.round; i++) {
        sequence.push(this.getRandomInt(4));
      }
      console.log(sequence);
      this.$emit("playSequence", sequence, this.timings[this.dLevel]);
    },

    getRandomInt(max) {
      return Math.floor(Math.random() * max);
    },
  },
};
</script>

<style lang="scss">
#app {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  & > * {
    margin: 20px;
  }
}
</style>
