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
    <div id="simon">
      <div id="center">
        <div id="controls">
          <a id="start" class="btn" @click="start"></a>
          <div id="counter">
            <span
              :class="{ lit: power }"
              v-text="showError ? 'NO' : timesUp ? 'TIME' : showCounter"
            ></span>
          </div>
        </div>
      </div>
      <div id="buttons">
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
    </div>
    <select v-model="delay" name="level">
      <option value="1500">Легкий</option>
      <option value="1000">Средний</option>
      <option value="400">Сложный</option>
    </select>
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

    click(tone) {
      if (this.userRow.length == 0) {
        this.currentDate = new Date().getTime();
      }
      if (!this.allowClick) return;

      if (new Date().getTime() - this.currentDate > Number(this.delay)) {
        this.timesUp = true;
        setTimeout(this.restart, 1000);
        return;
      }
      this.playTone(tone);
      this.userRow.push(tone);
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
      // Если это был последний клик
      if (this.userRow.length == this.gameRow.length) {
        let self = this;
        this.userRow = [];
        setTimeout(function() {
          self.addColor();
          self.playGameRow();
        }, 1000);
      }
    },
    start() {
      if (this.counter == 0 || this.allowClick) {
        this.restart();
        this.launched = true;
        this.addColor();
        this.playGameRow();
      }
    },
    addColor() {
      this.counter++;
      this.gameRow.push(this.randomTone());
    },
    playGameRow() {
      this.allowClick = false;
      this.isPlaying = true;
      let self = this;
      let delay = 1000;
      this.gameRow.forEach(function(tone, index, array) {
        if (index == array.length - 1)
          setTimeout(function() {
            if (self.launched) {
              self.playTone(tone);
              self.allowClick = true;
              self.isPlaying = false;
            }
          }, delay);
        else
          setTimeout(function() {
            self.playTone(tone);
          }, delay);
        delay += 1000;
      });
      this.isPlaying = false;
    },
    turnOffLight() {
      this.greenLight = false;
      this.redLight = false;
      this.yellowLight = false;
      this.blueLight = false;
    },
    // Plays the tone & highlights the color
    playTone(tone) {
      switch (tone) {
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
    randomTone() {
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
$color-green: darken(green, 5%);
$color-red: darken(red, 10%);
$color-yellow: darken(#ffd700, 10%);
$color-blue: darken(#0000cd, 5%);
$color-white: #fff;
$color-light-green: lighten(green, 5%);
$color-light-red: lighten(red, 5%);
$color-light-blue: lighten(#0000cd, 5%);
$color-light-yellow: lighten(#ffd700, 5%);
$color-bisque: #ffe4c4;

// Main Styles
body {
  font-family: "Open Sans", sans-serif;
  user-select: none;
  background-color: $color-bisque;
}
h1 {
  font-family: "Russo One", sans-serif;
  font-size: 3rem;
  cursor: default;
  margin: 1rem 0;
}
#simon {
  margin: 2rem auto 0 auto;
  max-width: 680px;
  min-width: 680px;
  max-height: 680px;
  min-height: 680px;
  background-color: $color-dark-gray;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  box-shadow: 0px 0px 18px rgba(0, 0, 0, 0.6);
}
#buttons {
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
}
#green {
  background-color: $color-green;
  margin-bottom: 20px;
  box-shadow: inset 3px 3px 10px rgba(255, 255, 255, 0.3);
}
#blue {
  background-color: $color-blue;
  box-shadow: inset -3px -3px 10px rgba(255, 255, 255, 0.3);
}
#yellow {
  background-color: $color-yellow;
  box-shadow: inset 3px -3px 10px rgba(255, 255, 255, 0.3);
}
#red {
  background-color: $color-red;
  margin-bottom: 20px;
  box-shadow: inset -3px 3px 10px rgba(255, 255, 255, 0.3);
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

// Center Controls
#center {
  border: 20px solid $color-dark-gray;
  box-sizing: border-box;
  position: absolute;
  min-width: 300px;
  max-width: 300px;
  min-height: 300px;
  max-height: 300px;
  background-color: $color-light-gray;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  color: $color-dark-gray;
}
#controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  min-width: 180px;
  margin-top: 0.5rem;
}
#counter {
  display: inline-block;
  width: 60px;
  height: 40px;
  color: $color-med-gray;
  background-color: $color-black;
  border: 4px solid $color-dark-gray;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 8px;
  font-size: 1.6rem;
}
.lit {
  color: $color-red;
}
#switch {
  cursor: pointer;
  display: inline-block;
  width: 50px;
  height: 25px;
  background-color: $color-dark-gray;
  position: relative;
}
#switch:after {
  content: "";
  position: absolute;
  top: 0;
  height: 19px;
  width: 19px;
  border: 3px solid $color-dark-gray;
  background-color: $color-red;
}
#switch.off:after {
  left: 0;
}
#switch.on:after {
  right: 0;
}
.btn {
  cursor: pointer;
  position: relative;
  display: inline-block;
  width: 24px;
  height: 24px;
  background-color: $color-yellow;
  border-radius: 50%;
  border: 4px solid $color-dark-gray;
  margin-top: 1rem;
  box-shadow: 2px 2px 3px rgba(0, 0, 0, 0.4);
}
.btn::after {
  position: absolute;
  top: -20px;
  left: -25%;
  font-size: 12px;
  text-transform: uppercase;
  font-weight: bold;
}
.btn:hover {
  background-color: $color-light-yellow;
}
#start.btn {
  background-color: $color-red;
}
#start.btn:hover {
  background-color: $color-light-red;
}
#start::after {
  content: "Start";
}

footer {
  padding: 3rem 0;
  text-align: center;
  font-size: 0.875rem;
}
</style>
