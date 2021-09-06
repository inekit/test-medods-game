<template>
  <div class="game-area">
    <button
      v-for="i in [0, 1, 2, 3]"
      :key="i"
      sound=""
      @click="press(i)"
      class="music-button"
      :class="{ pressed: pressed == i }"
      disabled
    />
  </div>
</template>

<script>
var sounds = [];
for (var i of [1, 2, 3, 4]) {
  sounds.push(new Audio(require("@/assets/sounds_" + i + ".mp3")));
}

export default {
  name: "Area",
  data() {
    return {
      position: 0,
      sounds,
      sequence: [],
      pressed: -1,
      timeout: "",
    };
  },

  methods: {
    press(bNumber) {
      // Анимация и звук нажатия
      this.sounds[bNumber].play();

      clearTimeout(this.timeout);
      this.pressed = bNumber;
      this.timeout = setTimeout(() => {
        this.pressed = -1;

        // Проверяем правильная ли кнопка нажата
        if (this.sequence[this.position] == bNumber) {
          if (this.position + 1 == this.sequence.length) this.stopRound(true);
          else this.position += 1;
        } else {
          this.stopRound(false);
        }
      }, 500);
    },

    stopRound(isWon) {
      // В родительском компоненте изменяем раунд (+1 или 1)
      this.$emit("stopRound", isWon);

      // Дизактивируем кнопки игры
      document
        .getElementsByClassName("game-area")[0]
        .getElementsByTagName("button")
        .forEach((el) => {
          el.disabled = true;
        });
    },
  },

  created() {
    this.$parent.$on("playSequence", (sequence, delay) => {
      this.position = 0;
      this.sequence = sequence;
      delay *= 1000;

      // Проигрываем последовательность звуков
      var i = 0;
      sequence.forEach((bId) => {
        i++;
        setTimeout(() => {
          this.sounds[bId].play();
          this.pressed = bId;
        }, delay * i);
      });

      // Активируем панель кнопок для начала раунда
      setTimeout(() => {
        this.pressed = -1;
        document
          .getElementsByClassName("game-area")[0]
          .getElementsByTagName("button")
          .forEach((el) => {
            console.log(el);
            el.disabled = false;
          });
      }, delay * (i + 1));
    });
  },
};
</script>

<style scoped lang="scss">
.game-area {
  width: 250px;
  height: 250px;
  border-radius: 125px;
  button.music-button {
    width: 50%;
    height: 50%;
    display: inline-block;
    &:nth-of-type(1) {
      background-color: lightblue;
      border-radius: 125px 0 0 0;
      &.pressed {
        background-color: blue;
      }
    }
    &:nth-of-type(2) {
      background-color: lightcoral;
      border-radius: 0 125px 0 0;
      &.pressed {
        background-color: coral;
      }
    }
    &:nth-of-type(3) {
      background-color: lightyellow;
      border-radius: 0 0 0 125px;
      &.pressed {
        background-color: yellow;
      }
    }
    &:nth-of-type(4) {
      background-color: lightgreen;
      border-radius: 0 0 125px 0;
      &.pressed {
        background-color: green;
      }
    }
  }
}
</style>
