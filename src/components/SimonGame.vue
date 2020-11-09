<template>
  <div class="main">
    <div class="main__title">Simon The Game</div>
    <div class="game__buttons">
      <div class="buttons__group_1">
        <div class="button_1" @click="clickGameButton(1)" :class="{ light: isActive[1]}"></div>
        <div class="button_2" @click="clickGameButton(2)" :class="{ light: isActive[2]}"></div>
      </div>
      <div class="buttons__group_2">
        <div class="button_3" @click="clickGameButton(3)" :class="{ light: isActive[3]}"></div>
        <div class="button_4" @click="clickGameButton(4)" :class="{ light: isActive[4]}"></div>
      </div>
    </div>
    <div class="game__start" @click="startTheGame">
      {{ startButton }}
      <div class="score" v-if="isPlaying">{{ showPlayerScore }}</div>
    </div>
    <div class="game__modes">
      <div class="game__modes__title">Modes:</div>
      <button :class="{ active: activeButtonMode === 'buttonMode1' }" @click="gameMode = 'easy'; activeButtonMode = 'buttonMode1'">Easy</button>
      <button :class="{ active: activeButtonMode === 'buttonMode2' }" @click="gameMode = 'normal'; activeButtonMode = 'buttonMode2'">Normal</button>
      <button :class="{ active: activeButtonMode === 'buttonMode3' }" @click="gameMode = 'hard'; activeButtonMode = 'buttonMode3'">Hard</button> 
    </div>
  </div>
</template>

<script>
export default {
  name: 'SimonGame',
  data() {
    return {
      startButton: "Start",
      gameMode: "normal",
      isPlaying: false,
      isClickable: false,
      isWin: false,
      colorSequence: [],
      colorSequenceInterval: null,
      playerColorSequence: [],
      score: 0,
      activeButtonMode: "buttonMode2",
      sounds: {
        1: "1",
        2: "2",
        3: "3",
        4: "4",
      },
      isActive: {
        1: false,
        2: false,
        3: false,
        4: false,
      }
    }
  },
  methods: {
    playButtonSound(key) {
      if(this.sounds[key]) {
        let audio = new Audio(require(`../assets/sounds/${this.sounds[key]}.mp3`));
        audio.play();
      }
    },
    startTheGame() {
      this.isPlaying = true;
      this.startButton = "Reset";
      this.isWin = false;
      this.colorSequence = [];
      this.playerColorSequence = [];
      this.score = 0;
      clearInterval(this.colorSequenceInterval);
      this.showColorSequence();
    },
    checkResult() {
      for(let i = 0; i < this.playerColorSequence.length; i++) {
        if(this.playerColorSequence[i] !== this.colorSequence[i]) {
          this.playerColorSequence = [];
          this.isPlaying = false;
          this.isClickable = false;
          this.startButton = "You are lose! Try again!";
          this.score = 0;
          setTimeout(() => {
            this.startButton = "Start";
          }, 1000);
        }
      }
      if(this.playerColorSequence.length === this.colorSequence.length) {
        this.playerColorSequence = [];
        this.score++;
        if(this.score === 5) {
          this.isWin = true;
          this.startButton = "You are the winner!";
          this.isClickable = false;
        } else {
          this.showColorSequence();
        }
      }
    },
    clickGameButton(key) {
      if(this.isClickable) {
        this.playButtonSound(key);
        this.highlight(key);
        this.playerColorSequence.push(key);
        this.checkResult();
      }
    },
    highlight(key) {
      this.isActive[key] = true;
      setTimeout(() => {
        this.isActive[key] = false;
      }, 300)
    },
    changeDifficulty(mode) {
      if(mode === "easy") {
        return 1500;
      } else if (mode === "normal") {
        return 1000;
      } else {
        return 400;
      }
    },
    showColorSequence() {
      let currentColorIndex = 0;
      let speed = this.colorSequence.length === 0 ? 1000 : this.changeDifficulty(this.gameMode);
      this.isClickable = false;
      this.colorSequence.push(Math.floor(Math.random() * 4 + 1));

      this.colorSequenceInterval = setInterval(() => {
        if(currentColorIndex >= this.colorSequence.length) {
          clearInterval(this.colorSequenceInterval);
          return this.isClickable = true;
        }
        this.playButtonSound(this.colorSequence[currentColorIndex]);
        this.highlight(this.colorSequence[currentColorIndex]);
        currentColorIndex++;
      }, speed)
    },
  },
  computed: {
    showPlayerScore() {
      if(this.isWin) {
        return "Will we play again?";
      }
      return "Score:" + this.score;
    }
  }
}
</script>


<style scoped>
.main {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}
.main__title {
  margin-bottom: 20px;
}
.game__buttons {
  width: 505px;
  height: 505px;
  border-radius: 50%;
  display: flex;
  flex-direction: column;
  background-color: rgb(0, 0, 0);
}
.buttons__group_1 {
  display: flex;
}
.buttons__group_2 {
  display: flex;
}
.buttons__group_1 div {
  border: 1px solid rgb(0, 0, 0);
  width: 250px;
  height: 250px
}

.buttons__group_2 div {
  border: 1px solid rgb(0, 0, 0);
  width: 250px;
  height: 250px
}
.buttons__group_1 div:hover {
  opacity: 0.5;
}
.buttons__group_2 div:hover {
  opacity: 0.5;
}
.button_1 {
  background-color: rgb(80, 28, 221);
  border-top-left-radius: 100%;
}
.button_2 {
  background-color: rgb(233, 58, 58);
  border-top-right-radius: 100%;
}
.button_3 {
  background-color: rgb(245, 245, 59);
  border-bottom-left-radius: 100%;
}
.button_4 {
  background-color: rgb(83, 240, 83);
  border-bottom-right-radius: 100%;
}
.button_1.light {
  background-color: rgb(32, 11, 88);
}
.button_2.light {
  background-color: rgb(107, 14, 14);
}
.button_3.light {
  background-color: rgb(95, 95, 11);
}
.button_4.light {
  background-color: rgb(7, 80, 7);
}
.game__start {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 15px;
  border-radius: 30px;
  background-color: rgb(129, 108, 187);
  width: 100px;
  cursor: pointer;
  margin-top: 40px;
}
.game__start:hover {
  opacity: 0.8;
}
.game__modes {
  text-align: center;
  margin-top: 20px;
}
.game__modes button {
  color: white;
  background: green;
  cursor: pointer;
  margin-left: 5px;
}
.game__modes button.active {
  background: black;
}
</style>
