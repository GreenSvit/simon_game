<template>
  <div id="app">
    <audio
      ref="sound1"
      src="https://s3.amazonaws.com/freecodecamp/simonSound1.mp3"
    ></audio>
    <audio
      ref="sound2"
      src="https://s3.amazonaws.com/freecodecamp/simonSound2.mp3"
    ></audio>
    <audio
      ref="sound3"
      src="https://s3.amazonaws.com/freecodecamp/simonSound3.mp3"
    ></audio>
    <audio
      ref="sound4"
      src="https://s3.amazonaws.com/freecodecamp/simonSound4.mp3"
    ></audio>
    <div class="game">
      <h1 class="game__title">Simon Game</h1>
      <div class="game__panel">
        <div
          class="button"
          :class="{ highlight: greenLight }"
          id="green"
          @click="click(1)"
        ></div>
        <div
          class="button"
          :class="{ highlight: redLight }"
          id="red"
          @click="click(2)"
        ></div>
        <div
          class="button"
          :class="{ highlight: yellowLight }"
          id="yellow"
          @click="click(3)"
        ></div>
        <div
          class="button"
          :class="{ highlight: blueLight }"
          id="blue"
          @click="click(4)"
        ></div>
      </div>
      <div class="game__controls">
        <a class="game__start" @click="start">New Game</a>
        <select class="game__level" v-model="delay" name="level">
          <option value="1500">Легкий</option>
          <option value="1000">Средний</option>
          <option value="400">Сложный</option>
        </select>
        <div class="game__counter">
          <span
            v-text="
              showError ? 'WRONG COLOR' : timesUp ? 'TIME IS UP' : showCounter
            "
          ></span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      launched: false, // Игра запущена ?
      timesUp: false, // Время вышло?
      currentDate: 0,
      counter: 0, // Счетчик
      gameRow: [], // Текущий рандомный игровой ряд
      isPlaying: false, // Проигрывается ли текущий рандомный игровой ряд?
      userRow: [], // Пользовательский игровой ряд
      redLight: false,
      greenLight: false,
      yellowLight: false,
      blueLight: false,
      allowClick: false, // Можно ли кликать ?
      showError: false,
      delay: "1500", // Текщее минимальное время, за которое юзер должен кликнуть (уровень сложности)
    };
  },
  computed: {
    // Показать счетчик
    showCounter() {
      return this.counter;
    },
  },
  methods: {
    restart() {
      // Перезапуск игры
      this.launched = false;
      this.timesUp = false;
      this.counter = 0;
      this.gameRow = [];
      this.userRow = [];
      this.allowClick = false;
      this.isPlaying = false;
      this.showError = false;
      this.greenLight = false;
      this.redLight = false;
      this.yellowLight = false;
      this.blueLight = false;
    },

    click(color) {
      if (this.userRow.length == 0) {
        this.currentDate = new Date().getTime();
      }
      if (!this.allowClick) return;

      if (new Date().getTime() - this.currentDate > Number(this.delay)) {
        this.timesUp = true;
        setTimeout(this.restart, 1000);
        return;
      }
      this.playSound(color);
      this.userRow.push(color);
      this.currentDate = new Date().getTime();
      // Ошибка
      if (
        this.userRow[this.userRow.length - 1] !=
        this.gameRow[this.userRow.length - 1]
      ) {
        this.showError = true;
        setTimeout(this.restart, 1000);
        return;
      }
      // Проверяем на последний клик
      if (this.userRow.length == this.gameRow.length) {
        let self = this;
        this.userRow = [];
        setTimeout(function() {
          self.addColor();
          self.playGameRow();
        }, 1000);
      }
    },
    // Старт игры
    start() {
      // Либо счетчик на нуле (игра не начата), либо в данный момент не проигрывется игровой ряд
      if (this.counter == 0 || this.allowClick) {
        this.restart();
        this.launched = true;
        this.addColor();
        this.playGameRow();
      }
    },
    addColor() {
      this.counter++;
      this.gameRow.push(this.randomColor());
    },
    playGameRow() {
      this.allowClick = false;
      this.isPlaying = true;
      let self = this;
      let playDelay = 1000;
      this.gameRow.forEach(function(color, index, array) {
        if (index == array.length - 1)
          setTimeout(function() {
            if (self.launched) {
              self.playSound(color);
              self.allowClick = true;
              self.isPlaying = false;
            }
          }, playDelay);
        else
          setTimeout(function() {
            self.playSound(color);
          }, playDelay);
        playDelay += 1000;
      });
      this.isPlaying = false;
    },
    turnOffLight() {
      this.greenLight = false;
      this.redLight = false;
      this.yellowLight = false;
      this.blueLight = false;
    },
    // Подсвечиваем блок и проигрываем звук
    playSound(color) {
      switch (color) {
        case 1:
          this.$refs.sound1.pause();
          this.$refs.sound1.currentTime = 0;
          this.$refs.sound1.play();
          this.greenLight = true;
          break;
        case 2:
          this.$refs.sound2.pause();
          this.$refs.sound2.currentTime = 0;
          this.$refs.sound2.play();
          this.redLight = true;
          break;
        case 3:
          this.$refs.sound3.pause();
          this.$refs.sound3.currentTime = 0;
          this.$refs.sound3.play();
          this.yellowLight = true;
          break;
        case 4:
          this.$refs.sound4.pause();
          this.$refs.sound4.currentTime = 0;
          this.$refs.sound4.play();
          this.blueLight = true;
          break;
      }
      setTimeout(this.turnOffLight, 400);
    },
    randomColor() {
      return Math.floor(Math.random() * 4) + 1;
    },
  },
};
</script>

<style lang="scss">
// Colors
$color-black: #000;
$color-dark-gray: #2e2e2e;
$color-med-gray: #666;
$color-light-gray: #ccc;
$color-green: darken(#6ccc49, 5%);
$color-red: darken(#ef6055, 10%);
$color-yellow: darken(#efe155, 10%);
$color-blue: darken(#54e3f1, 5%);
$color-white: #fff;
$color-light-green: lighten(#6ccc49, 5%);
$color-light-red: lighten(#ef6055, 5%);
$color-light-blue: lighten(#54e3f1, 5%);
$color-light-yellow: lighten(#efe155, 5%);
$bgBlue: #11192a;

// Mixins

@mixin gameControl {
  font-size: 16px;
  text-transform: uppercase;
  padding: 10px 16px 8px;
  border-radius: 3px;
  text-transform: uppercase;
}

// Main Styles
body {
  font-family: "Open Sans", sans-serif;
  user-select: none;
  background-color: $bgBlue;
}
.game {
  margin: 0 auto;
  max-width: 620px;
  min-width: 620px;
  max-height: 620px;
  min-height: 620px;
  position: relative;
}

.game__title {
  color: $color-blue;
  text-align: center;
}
.game__panel {
  display: flex;
  flex-wrap: wrap;
  max-width: 620px;
  min-width: 620px;
  justify-content: space-between;
}
.button {
  cursor: pointer;
  min-width: 300px;
  min-height: 300px;
  max-width: 300px;
  max-height: 300px;
  border-radius: 10px;
}
#green {
  background-color: $color-green;
  margin-bottom: 20px;
}
#blue {
  background-color: $color-blue;
}
#yellow {
  background-color: $color-yellow;
}
#red {
  background-color: $color-red;
  margin-bottom: 20px;
}
#green.highlight {
  background-color: $color-light-green;
}
#red.highlight {
  background-color: $color-light-red;
}
#blue.highlight {
  background-color: $color-light-blue;
}
#yellow.highlight {
  background-color: $color-light-yellow;
}

// Controls
.game__controls {
  box-sizing: border-box;
  min-width: 300px;
  color: $color-dark-gray;
  display: flex;
  justify-content: space-between;
  align-items: center;
  min-width: 180px;
  margin-top: 40px;
  padding-left: 30px;
  padding-right: 30px;
}

.game__counter {
  @include gameControl;
  padding-left: 0px;
  padding-right: 0px;
  font-size: 18px;
  font-weight: bold;
  min-width: 150px;
  max-width: 150px;
  color: darken($color-green, 40%);
  background-color: $color-green;
  border: 3px solid darken($color-green, 30%);
  display: flex;
  align-items: center;
  justify-content: center;
}

.game__start {
  @include gameControl;
  cursor: pointer;
  background-color: $color-red;
  color: white;
  border: 3px solid darken($color-red, 20%);

  &:hover {
    background-color: lighten($color-red, 15%);
  }

  &:active {
    opacity: 0.6;
  }
}

.game__level {
  @include gameControl;
  background-color: lighten($color-blue, 25%);
  border: 3px solid darken($color-blue, 40%);
}
</style>
