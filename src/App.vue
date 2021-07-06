<template>
  <div class="total-score">
    <div class="total-score-top">
      <h2>Total score</h2>
      <div class="total-score-window">
        <div class="cross-score total-score-inner">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="32"
            height="32"
            fill="currentColor"
            class="bi bi-x-lg"
            viewBox="0 0 16 16"
          >
            <path
              d="M1.293 1.293a1 1 0 0 1 1.414 0L8 6.586l5.293-5.293a1 1 0 1 1 1.414 1.414L9.414 8l5.293 5.293a1 1 0 0 1-1.414 1.414L8 9.414l-5.293 5.293a1 1 0 0 1-1.414-1.414L6.586 8 1.293 2.707a1 1 0 0 1 0-1.414z"
            />
          </svg>
          <h3 class="score-number">{{ totalScore.cross }}</h3>
        </div>
        <div class="circle-score total-score-inner">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="32"
            height="32"
            fill="currentColor"
            class="bi bi-circle"
            viewBox="0 0 16 16"
          >
            <path
              d="M8 15A7 7 0 1 1 8 1a7 7 0 0 1 0 14zm0 1A8 8 0 1 0 8 0a8 8 0 0 0 0 16z"
            />
          </svg>
          <h3 class="score-number">{{ totalScore.circle }}</h3>
        </div>
      </div>
    </div>
    <button @click="clearScore()" class="total-score-bottom clear-score-btn">
      Clear score
    </button>
  </div>
  <div class="main">
    <div id="field">
      <div v-for="(outher, i) in field" :key="outher" class="outher">
        <button
          @click="setMark(i, j)"
          v-for="(cell, j) in outher"
          :key="cell"
          class="cell"
          v-bind:disabled="isGameFinished"
        >
          <div v-if="cell === 'X'" class="cross" v-bind:class="'cross' + i + j">
            <div class="cross-line cross-line-1"></div>
            <div class="cross-line cross-line-2"></div>
          </div>

          <div v-if="cell === 'O'" class="circle"></div>
        </button>
      </div>
    </div>
    <template v-if="isGameFinished">
      <h4>Game has finished, winner: {{ winner }}</h4>
      <button @click="clearField()" class="clear-field-btn">
        reset
      </button>
    </template>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      field: [
        ['', '', ''],
        ['', '', ''],
        ['', '', ''],
      ],
      isCrossPlayer: true,
      isGameFinished: false,
      winner: null,
      totalScore: {
        cross: 0,
        circle: 0,
      },
    };
  },
  mounted() {
    const gameData = this.getGameDataFromLocalStorage('tic-tac-toe');
    this.field = gameData.field;
    this.isCrossPlayer = gameData.isCrossPlayer;
    this.isGameFinished = gameData.isGameFinished;
    this.winner = gameData.winner;
    this.totalScore = gameData.totalScore;
  },
  methods: {
    getGameDataFromLocalStorage(key) {
      return JSON.parse(localStorage.getItem(key)) || this.$data;
    },
    setGameDataToLocalStorage(key) {
      localStorage.setItem(
        key,
        JSON.stringify({
          field: this.field,
          isCrossPlayer: this.isCrossPlayer,
          isGameFinished: this.isGameFinished,
          winner: this.winner,
          totalScore: this.totalScore,
        })
      );
    },
    setMark(i, j) {
      let copy = [...this.field];
      if (!this.field[i][j]) {
        if (this.isCrossPlayer) {
          copy[i][j] = 'X';
          this.field = copy;
        } else {
          copy[i][j] = 'O';
          this.field = copy;
        }
        this.isCrossPlayer = !this.isCrossPlayer;
        this.checkWinPositions();
      }
    },
    checkWinPositions() {
      let winPos = [],
        line1 = '',
        line2 = '',
        line3 = '',
        line4 = '',
        line5 = '',
        line6 = '';
      for (let i = 0; i < 3; i++) {
        line1 += this.field[0][i];
        line2 += this.field[1][i];
        line3 += this.field[2][i];

        line4 += this.field[i][0];
        line5 += this.field[i][1];
        line6 += this.field[i][2];
      }

      const diag1 = this.field[0][0] + this.field[1][1] + this.field[2][2];
      const diag2 = this.field[0][2] + this.field[1][1] + this.field[2][0];

      winPos.push(line1, line2, line3, line4, line5, line6, diag1, diag2);

      if (winPos.some((item) => item === 'XXX' || item === 'OOO')) {
        this.isGameFinished = true;
        if (this.isCrossPlayer) {
          this.winner = 'O';
          this.totalScore.circle += 1;
        } else if (!this.isCrossPlayer) {
          this.winner = 'X';
          this.totalScore.cross += 1;
        }
      } else if (
        this.field.every((innerArray) => innerArray.every((el) => el !== ''))
      ) {
        this.isGameFinished = true;
        this.winner = 'draw';
      }
    },
    clearScore() {
      this.totalScore = {
        cross: 0,
        circle: 0,
      };
    },
    clearField() {
      this.field = [
        ['', '', ''],
        ['', '', ''],
        ['', '', ''],
      ];
      this.isCrossPlayer = true;
      this.isGameFinished = false;
      this.winner = null;
    },
  },
  watch: {
    field() {
      this.setGameDataToLocalStorage('tic-tac-toe');
    },
    totalScore() {
      this.setGameDataToLocalStorage('tic-tac-toe');
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  display: flex;
  justify-content: center;
}

#field {
  width: min-content;
  display: flex;
  flex-wrap: wrap;
  border-top: 2px solid #1f2937;
  border-left: 2px solid #1f2937;
  margin: 0 auto;
}

.total-score-inner {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.total-score-window {
  display: flex;
  justify-content: space-between;
}

.score-number {
  font-size: 1.5em;
  font-weight: bold;
}

.cell {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 140px;
  height: 140px;
  border-bottom: 2px solid #1f2937;
  border-right: 2px solid #1f2937;
  cursor: pointer;
  background: none;
}

.cell:hover {
  box-shadow: 0px 5px 10px 2px rgba(4, 0, 255, 0.2) inset;
}

.outher {
  display: flex;
}

.clear-field-btn {
  font-family: 'Zen Loop', cursive;
  font-size: 1.5em;
  text-transform: uppercase;
  width: 145px;
  height: 1.8em;
  padding: 0.2em 1.5em;
  outline: none;
  border: none;
  background: rgb(2, 0, 36);
  background: linear-gradient(
    180deg,
    rgba(2, 0, 36, 1) 0%,
    rgba(9, 121, 61, 1) 92%
  );
  color: whitesmoke;
  border-radius: 5px;
  cursor: pointer;
}

.clear-field-btn:hover {
  opacity: 0.9;
}

.cross {
  width: 100%;
  height: 100%;
  position: relative;
  display: flex;
  justify-content: center;
}

.circle {
  width: 80px;
  height: 80px;
  border: double 1em transparent;
  border-radius: 50%;
  background-image: linear-gradient(white, white),
    linear-gradient(90deg, rgba(2, 0, 36, 1) 0%, rgba(121, 9, 47, 1) 35%);
  background-origin: border-box;
  background-clip: content-box, border-box;
}

.cross-line {
  top: 50%;
  bottom: 50%;
  position: absolute;
  width: 130px;
  height: 18px;
  border-radius: 7px;
  background: rgb(2, 0, 36);
  background: linear-gradient(
    90deg,
    rgba(2, 0, 36, 1) 0%,
    rgba(9, 85, 121, 1) 35%
  );
}

.total-score-inner:first-child {
  border-right: 1px solid #1f2937;
  padding-right: 0.8em;
}

.cross-line-1 {
  transform: rotate(45deg);
}

.cross-line-2 {
  transform: rotate(-45deg);
}

.total-score {
  position: absolute;
  left: 0;
  display: flex;
  flex-direction: column;
  font-family: 'Zen Loop', cursive;
  font-size: 1.5em;
  color: #111827;
}

.total-score-top {
  padding: 0.5em 1.5em;
  border-right: 2px solid #111827;
  border-top: 2px solid #111827;
  border-bottom: 2px solid #111827;
  border-top-right-radius: 0.7em;
  border-bottom-right-radius: 0.7em;
  margin-bottom: 10px;
}

.clear-score-btn {
  outline: none;
  border: none;
  background: none;
  font-family: 'Zen Loop', cursive;
  font-size: 1.2em;
  border-right: 2px solid #111827;
  border-top: 2px solid #111827;
  border-bottom: 2px solid #111827;
  border-top-right-radius: 0.4em;
  border-bottom-right-radius: 0.4em;
  padding: 0.2em 0.7em;
  cursor: pointer;
}

.clear-score-btn:hover {
  box-shadow: 0px 5px 10px 2px rgba(4, 0, 255, 0.2) inset;
  transition: 0.2s;
  transform: scale(1.07);
}
</style>
