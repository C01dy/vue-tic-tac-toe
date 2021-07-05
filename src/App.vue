<template>
  <div id="field">
    <div v-for="(outher, i) in field" :key="outher" class="outher">
      <div
        @click="setMark(i, j)"
        v-for="(cell, j) in outher"
        :key="cell"
        class="cell"
      >
        <div v-if="cell === 'X'" class="cross">
          <div class="cross-line cross-line-1"></div>
          <div class="cross-line cross-line-2"></div>
        </div>

        <div v-if="cell === 'O'" class="circle"></div>
      </div>
    </div>
  </div>
  <template v-if="isGameFinished">
    <h4>Game has finished, winner: {{ winner }}</h4>
    <button @click="clearField()" class="clear-field-btn">
      Clear field
    </button>
  </template>
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
    };
  },
  methods: {
    setMark(i, j) {
      if (!this.field[i][j]) {
        if (this.isCrossPlayer) {
          this.field[i][j] = 'X';
        } else {
          this.field[i][j] = 'O';
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
        } else {
          this.winner = 'X';
        }
      }
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
}

#field {
  width: min-content;
  /* height: 423px; */
  display: flex;
  flex-wrap: wrap;
  border-top: 1px solid black;
  border-left: 1px solid black;
  margin: 0 auto;
}

.cell {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 140px;
  height: 140px;
  border-bottom: 1px solid black;
  border-right: 1px solid black;
  cursor: pointer;
}

.cell:hover {
  box-shadow: 0px 5px 10px 2px rgba(4, 0, 255, 0.2) inset;
}

.outher {
  display: flex;
}

.clear-field-btn {
  width: 145px;
  height: 3em;
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

.cross-line-1 {
  transform: rotate(45deg);
}

.cross-line-2 {
  transform: rotate(-45deg);
}
</style>
