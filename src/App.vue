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
              v-text="showError ? 'NO' : displayCount"
            ></span>
          </div>
        </div>
      </div>
      <div id="buttons">
        <div
          class="button"
          :class="{ highlight: hlGreen }"
          id="green"
          @click="input(1)"
        ></div>
        <div
          class="button"
          :class="{ highlight: hlRed }"
          id="red"
          @click="input(2)"
        ></div>
        <div
          class="button"
          :class="{ highlight: hlYellow }"
          id="yellow"
          @click="input(3)"
        ></div>
        <div
          class="button"
          :class="{ highlight: hlBlue }"
          id="blue"
          @click="input(4)"
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
      started: false, // Has the game started?
      count: 0, // What's the current count?
      series: [], // The current series of tones
      playingSeries: false, // Is the game currently outputting the series of tones?
      userInput: [],
      hlRed: false,
      hlGreen: false,
      hlYellow: false,
      hlBlue: false,
      allowInput: false,
      showError: false,
      delay: "1500",
      currentDate: 0,
    };
  },
  computed: {
    displayCount() {
      return this.count;
    },
  },
  methods: {
    reset() {
      // Reset the game state
      this.started = false;
      this.count = 0;
      this.series = [];
      this.userInput = [];
      this.allowInput = false;
      this.playingSeries = false;
      this.showError = false;
      this.hlGreen = false;
      this.hlRed = false;
      this.hlYellow = false;
      this.hlBlue = false;
    },

    input(tone) {
      if (this.count == 1) {
        this.currentDate = new Date().getTime();
      }
      if (new Date().getTime() - this.currentDate < Number(this.delay)) {
        if (!this.allowInput) return;
        this.playTone(tone);
        this.userInput.push(tone);
        // Check if this was the wrong input
        if (
          this.userInput[this.userInput.length - 1] !=
          this.series[this.userInput.length - 1]
        ) {
          this.showError = true;
          setTimeout(this.reset, 3000);
          return;
        }
        // If this was the final input
        if (this.userInput.length == this.series.length) {
          let self = this;
          this.userInput = [];
          setTimeout(function () {
            self.addTone();
            self.playSeries();
          }, 1000);
        }
      }
    },
    start() {
      if (this.count == 0 || this.allowInput) {
        this.reset();
        this.started = true;
        this.addTone();
        this.playSeries();
      }
    },
    addTone() {
      this.count++;
      this.series.push(this.randomTone());
    },
    playSeries() {
      this.allowInput = false;
      this.playingSeries = true;
      let self = this;
      let delay = 1000;
      this.series.forEach(function (tone, index, array) {
        if (index == array.length - 1)
          setTimeout(function () {
            if (self.started) {
              self.playTone(tone);
              self.allowInput = true;
              self.playingSeries = false;
            }
          }, delay);
        else
          setTimeout(function () {
            self.playTone(tone);
          }, delay);
        delay += 1000;
      });
      this.playingSeries = false;
    },
    clearHighlights() {
      this.hlGreen = false;
      this.hlRed = false;
      this.hlYellow = false;
      this.hlBlue = false;
    },
    // Plays the tone & highlights the color
    playTone(tone) {
      switch (tone) {
        case 1:
          this.$refs.sound1.pause();
          this.$refs.sound1.currentTime = 0;
          this.$refs.sound1.play();
          this.hlGreen = true;
          break;
        case 2:
          this.$refs.sound2.pause();
          this.$refs.sound2.currentTime = 0;
          this.$refs.sound2.play();
          this.hlRed = true;
          break;
        case 3:
          this.$refs.sound3.pause();
          this.$refs.sound3.currentTime = 0;
          this.$refs.sound3.play();
          this.hlYellow = true;
          break;
        case 4:
          this.$refs.sound4.pause();
          this.$refs.sound4.currentTime = 0;
          this.$refs.sound4.play();
          this.hlBlue = true;
          break;
      }
      setTimeout(this.clearHighlights, 750);
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
  border-radius: 50%;
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
  border-top-left-radius: 300px;
  margin-bottom: 20px;
  box-shadow: inset 3px 3px 10px rgba(255, 255, 255, 0.3);
}
#blue {
  background-color: $color-blue;
  border-bottom-right-radius: 300px;
  box-shadow: inset -3px -3px 10px rgba(255, 255, 255, 0.3);
}
#yellow {
  background-color: $color-yellow;
  border-bottom-left-radius: 300px;
  box-shadow: inset 3px -3px 10px rgba(255, 255, 255, 0.3);
}
#red {
  background-color: $color-red;
  border-top-right-radius: 300px;
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
  border-radius: 50%;
  background-color: $color-light-gray;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  color: $color-dark-gray;
}
#title {
  margin-top: 20px;
  text-align: center;
  background-color: $color-light-gray;
  width: 200px;
  border-top-left-radius: 50%;
  border-top-right-radius: 50%;
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
