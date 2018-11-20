<template>
  <section id="app">
      <header class="header">
        <div class="wrapper">
          <div class="header__align">
            <h1>Reversi</h1>
            <h2 v-if="grid.length > 0">PLayer Turn : {{ player === 1 ? 'Noir' : 'Blanc'}}</h2>
            <button @click="NewGame">New Game</button>
          </div>
        </div>
      </header>

      <div class="wrapper">
        <article class="grid">
          <div class="row" v-for="(item,i ) in grid" v-if="grid.length > 0" :key="i">
            <Square
              v-for="elmt in item"
              :status="elmt"
              @clickSquare="checkElement($event)"
              v-if="elmt != null"
              :key="elmt.status.position"/>
          </div>
        </article>
      </div>
  </section>
</template>

<script>
import Square from './components/Square.vue';

export default {
  name: 'app',
  data() {
    return {
      grid: [],
      row: 8,
      col: 8,
      player: 1,
    };
  },
  components: {
    Square,
  },
  methods: {
    initGrid() {
      const { row, col } = this;
      this.grid = [];

      const grill = [];

      for (let i = 1; i <= row; i += 1) {
        grill[i] = [];

        for (let j = 1; j <= col; j += 1) {
          grill[i][j] = { status: 0, position: [i, j] };
        }
      }
      return grill;
    },
    NewGame() {
      const grill = this.initGrid();

      grill[4][4].status = 2;
      grill[4][5].status = 1;
      grill[5][4].status = 1;
      grill[5][5].status = 2;

      this.grid = grill;
    },
    checkElement(data) {
      const { position, status } = data;

      const positionSquare = position;
      const statusSquare = status;
      const player = this.player;
      if (!this.isEmpty(status)) {
        return;
      }

      this.isValidPosition(position, player);
    },
    isEmpty(data) {
      return data === 0;
    },
    isValidPosition(data, player) {
      const gridCheck = this.grid;
      const row = data[0];
      const col = data[1];
      const status = player;
      const statusReverse = player === 1 ? 2 : 1;
      let rowCheck;
      let colCheck;
      const squarePosition = [];

      for (let rowDir = -1; rowDir <= 1; rowDir += 1) {
        for (let colDir = -1; colDir <= 1; colDir += 1) {
          rowCheck = row + rowDir;
          colCheck = col + colDir;

          if (this.positionInGrid(rowCheck, colCheck)) {
            if (this.checkStatus(rowCheck, colCheck) && this.checkPlayer(rowCheck, colCheck, statusReverse)) {
              squarePosition.push([rowCheck, colCheck]);
            }
          }
        }
      }

      this.checkAround(squarePosition, [row, col], player);
    },
    checkAround(data, event, player) {
      const playerInverse = player === 1 ? 2 : 1;
      const dir = [];

      for (let i = 0; i < data.length; i += 1) {
        let x = data[i][0] - event[0];
        let y = data[i][1] - event[1];

        // check if next element in line is no empty
        if (this.checkStatus(data[i][0] - -x, data[i][1] - -y)) {
          // check if next element is ours
          if (this.checkPlayer(data[i][0] - -x, data[i][1] - -y, player)) {
            this.setColor(data[i][0], data[i][1], player);
            this.setColor(event[0], event[1], player);
            this.changePlayer(player);
          }
          const solution = [data[i][0], data[i][1]];
          const newItemX = data[i][0] - -x;
          const newItemY = data[i][1] - -y;

          if (this.checkPlayer(data[i][0] - -x, data[i][1] - -y, playerInverse)) {
            const arrayLine = [];

            while (
              this.checkPlayer(data[i][0] - -x, data[i][1] - -y, playerInverse) &&
              this.checkStatus(data[i][0] - -x, data[i][1] - -y)
            ) {
              arrayLine.push([data[i][0] - -x, data[i][1] - -y]);
              x += x;
              y += y;
            }

            // TODO: need to corrige it cos sometimes pb

            for (let i = 0; i < arrayLine.length; i += 1) {
              this.setColor(arrayLine[i][0], arrayLine[i][1], player);
              this.setColor(solution[0], solution[1], player);
              this.setColor(newItemX, newItemY, player);
              this.setColor(event[0], event[1], player);
            }

            this.changePlayer(player);
          }
        }
      }
    },
    checkStatus(row, col) {
      const gridCheck = this.grid;
      return gridCheck[row][col].status !== 0;
    },
    checkPlayer(row, col, player) {
      const gridCheck = this.grid;
      return gridCheck[row][col].status === player;
    },
    setColor(row, col, player) {
      this.grid[row][col].status = player;
    },
    positionInGrid(row, col) {
      return row >= 1 && row <= this.row && (col >= 1 && col <= this.col);
    },
    changePlayer(player) {
      this.player = player === 1 ? 2 : 1;
    },
  },
};
</script>

<style lang="scss">
.header {
  border-bottom: 1px solid black;
  color: white;
  height: 6rem;

  .wrapper {
    height: 100%;
  }

  &__align {
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    width: 100%;
  }
}

button {
  background-color: #ff2462;
  border: 0;
  color: #fff;
  margin-bottom: 3rem;
  padding: 10px 26px;
}

.grid {
  display: flex;
  max-width: 60rem;
  margin-left: auto;
  margin-right: auto;
  flex-direction: column;

  .row {
    display: flex;
    width: 100%;
  }
}
</style>
