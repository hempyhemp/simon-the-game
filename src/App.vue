<template>
  <div class="container">
    <div class="text">
      <h1>Simon The Game</h1>
    </div>

    <div class="text" v-if="!this.gameStarted">
      <base-button class="startButton" @click="onStart()" text="Start Game">
      </base-button>
    </div>

    <div class="text" v-if="this.difficultySelected && !this.gameStarted">
      <base-button
        class="setDifficulty"
        text="Difficulty"
        @click="onSetDifficulty()"
      >
      </base-button>
    </div>

    <div class="text" v-if="!this.difficultySelected && !this.gameStarted">
      <h4>Select difficulty</h4>
      <base-button
        class="difficulty"
        text="Easy"
        @click="onChangeDifficulty(DIFFICULTY.EASY)"
      >
      </base-button>

      <base-button
        class="difficulty"
        text="Medium"
        @click="onChangeDifficulty(DIFFICULTY.MEDIUM)"
      >
      </base-button>

      <base-button
        class="difficulty"
        text="Hard"
        @click="onChangeDifficulty(DIFFICULTY.HARD)"
      >
      </base-button>
    </div>

    <div class="text" v-show="gameStarted">Score : {{ iteration }}</div>

    <div class="blocksRoot" v-show="gameStarted">
      <div class="grid">
        <base-game-block
          v-for="i in 4"
          :key="i"
          :block-type="i - 1"
          :active="state[i - 1]"
          @click.native="onClick(i - 1)"
        />
      </div>
    </div>

    <div class="text" v-show="gameLoosed">
      <span class="loose">You Loose</span>
      <span>High Score : {{ score }}</span>
    </div>
  </div>
</template>

<script>
import BaseGameBlock from "@/components/BaseGameBlock.vue";
import BaseButton from "@/components/BaseButton.vue";
import Vue from "vue";

import startSound from "@/sounds/startSound.mp3";
import looseSound from "@/sounds/looseSound.mp3";
import sound1 from "@/sounds/sound1.mp3";
import sound2 from "@/sounds/sound2.mp3";
import sound3 from "@/sounds/sound3.mp3";
import sound4 from "@/sounds/sound4.mp3";

const DIFFICULTY = {
  EASY: 0,
  MEDIUM: 1,
  HARD: 2,
};

export default {
  name: "App",
  components: {
    BaseGameBlock,
    BaseButton,
  },

  methods: {
    onClick(id) {
      this.setState(id, true);
      Vue.set(this.clientSubsequence, this.clientIteration, id);
      this.clientIteration++;
      this.playSound(this.blockSounds[id]);
      this.continueGame();
    },

    setState(id, state) {
      Vue.set(this.state, id, state);

      setTimeout(() => {
        for (let i = 0; i < this.state.length; i++) {
          Vue.set(this.state, i, false);
        }
      }, 400);
    },

    onStart() {
      this.playSound(startSound);
      this.continueGame();
      this.gameStarted = true;
      this.gameLoosed = false;
    },

    nextState() {
      this.pushRandArray();
      this.iteration++;

      for (let i = 0; i < this.iteration; i++) {
        setTimeout(() => {
          this.setState(this.subsequence[i], true);
          this.playSound(this.blockSounds[this.subsequence[i]]);
        }, this.currentDifficultyValue * (i + 1));
      }
    },

    canContinue() {
      return this.equalArrays(this.subsequence, this.clientSubsequence);
    },

    continueGame() {
      if (this.canContinue()) {
        setTimeout(() => {
          this.nextState();
        }, 1000);

        this.clientSubsequence = [];
        this.clientIteration = 0;
      }

      if (
        this.compareElements(
          this.subsequence,
          this.clientSubsequence,
          this.clientIteration - 1
        )
        // this.subsequence[this.clientIteration - 1] !=
        // this.clientSubsequence[this.clientIteration - 1]
      ) {
        this.loseGame();
      }
    },

    loseGame() {
      if (this.iteration > this.score) {
        this.score = this.iteration;
      }
      this.playSound(looseSound);
      this.clientSubsequence = [];
      this.clientIteration = 0;
      this.subsequence = [];
      this.iteration = 0;
      this.gameStarted = false;
      this.gameLoosed = true;
    },

    pushRandArray() {
      for (let i = 0; i < 3; ++i) {
        Vue.set(this.subsequence, this.iteration, this.getRandomInt(0, 3));
      }
    },

    getRandomInt(min, max) {
      min = Math.ceil(min);
      max = Math.floor(max) + 1;
      return Math.floor(Math.random() * (max - min)) + min;
    },

    equalArrays(a, b) {
      if (a.length != b.length) return false;

      for (var i = 0; i < a.length; i++) if (a[i] !== b[i]) return false;
      return true;
    },

    compareElements(arr1, arr2, id) {
      return arr1[id] != arr2[id];
    },

    playSound(sound) {
      if (sound) {
        let audio = new Audio(sound);
        audio.preload;
        audio.play();
      }
    },

    onChangeDifficulty(id) {
      this.currentDifficultyValue = this.difficulty[id].value;
      this.difficultySelected = true;
    },

    onSetDifficulty() {
      this.difficultySelected = false;
    },
  },

  data() {
    const difficulty = [
      {
        value: 1500,
      },
      {
        value: 1000,
      },
      {
        value: 400,
      },
    ];

    let currentDifficultyValue = difficulty[0].value;

    return {
      DIFFICULTY,
      state: [false, false, false, false],

      subsequence: [],
      iteration: 0,

      clientSubsequence: [],
      clientIteration: 0,

      score: 0,
      gameStarted: false,
      gameLoosed: false,
      difficultySelected: false,

      difficulty,

      currentDifficultyValue,

      blockSounds: [sound1, sound2, sound3, sound4],
    };
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Palette+Mosaic&display=swap");
@import "@/scss/variables";

* {
  box-sizing: border-box;
}

.container {
  max-width: 700px;
  margin: 0 auto;
  margin-top: 50px;
}

.text {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
  margin-bottom: 40px;
}

html,
body {
  font-size: 28px;
  font-family: $font-family;
  color: $font-color;
  margin: 0;
  padding: 0;
}

.blocksRoot {
  height: $block-size * 2;
  width: $block-size * 2;
  margin: 0 auto;
  margin-top: 150px;
  margin-bottom: 150px;
}

.grid {
  display: grid;
  flex-wrap: wrap;
  justify-content: center;
  grid-template-columns: repeat(2, 0fr);
  gap: 10px;
  transform: rotate(45deg);
}

.loose {
  margin-top: 30px;
  font-size: 30px;
  color: $error-color;
}

.startButton {
  --button-width: 280px;
  margin-top: 15%;
}

.difficulty {
  --button-width: 100px;
  --button-height: 35px;
  --button-font-size: 20px;
  margin-top: 20px;
}

.setDifficulty {
  --button-width: 150px;
  --button-height: 45px;
  --button-font-size: 20px;
  margin-top: 30px;
}
</style>
