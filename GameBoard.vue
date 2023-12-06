<template>
  <div>
    <div class="game-board">
      <div v-for="(row, rowIndex) in board" :key="rowIndex" class="row">
        <div v-for="(cell, colIndex) in row" :key="colIndex" :class="getCellClasses(rowIndex, colIndex)" @click="makeMove(rowIndex, colIndex)">
          {{ cell }}
        </div>
      </div>
    </div>
    <div class="status">
      <p v-if="winner">{{ winner }} wins!</p>
      <p v-else-if="isDraw">It's a draw!</p>
      <p v-else>Current Player: {{ currentPlayer }}</p>
    </div>
    <button v-if="winner || isDraw" @click="restartGame">Restart</button>
  </div>
</template>

<script>
/* eslint-disable */
export default {
  data() {
    return {
      board: [
        ['', '', ''],
        ['', '', ''],
        ['', '', '']
      ],
      currentPlayer: 'X',
      winner: '',
      isDraw: false
    };
  },
  methods: {
    makeMove(row, col) {
      if (this.board[row][col] === '' && !this.winner && !this.isDraw) {
        this.board[row][col] = this.currentPlayer;
        this.checkWin();
        this.checkDraw();
        this.currentPlayer = this.currentPlayer === 'X' ? 'O' : 'X';

        this.makeAIMove();
      }
    },
    makeAIMove() {
      if (!this.winner && !this.isDraw) {
        for (let row = 0; row < 3; row++) {
          for (let col = 0; col < 3; col++) {
            if (this.board[row][col] === '') {
              this.board[row][col] = this.currentPlayer;
              this.checkWin();
              this.checkDraw();
              this.currentPlayer = this.currentPlayer === 'X' ? 'O' : 'X';
              return;
            }
          }
        }
      }
    },
    checkWin() {
      const winningCombinations = [
        // Rows
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        // Columns
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        // Diagonals
        [0, 4, 8],
        [2, 4, 6]
      ];

      for (let combination of winningCombinations) {
        const [a, b, c] = combination;
        if (
          this.board[Math.floor(a / 3)][a % 3] === this.currentPlayer &&
          this.board[Math.floor(b / 3)][b % 3] === this.currentPlayer &&
          this.board[Math.floor(c / 3)][c % 3] === this.currentPlayer
        ) {
          this.winner = this.currentPlayer;
          break;
        }
      }
    },
    getCellClasses(rowIndex, colIndex) {
      const cell = this.board[rowIndex][colIndex];
      const isWinningCell = this.isWinningCell(rowIndex, colIndex);

      return {
        cell: true,
        'cell-x': cell === 'X',
        'cell-o': cell === 'O',
        'cell-win': isWinningCell
      };
    },

    isWinningCell(rowIndex, colIndex) {
  if (this.winner) {
    const winningCombinations = [
      // Замените это на свои реальные комбинации выигрышных клеток
      // Например, координаты ячеек для выигрышных комбинаций
      [[0, 0], [0, 1], [0, 2]], // Первая строка
      [[1, 0], [1, 1], [1, 2]], // Вторая строка
      [[2, 0], [2, 1], [2, 2]], // Третья строка
      [[0, 0], [1, 0], [2, 0]], // Первый столбец
      [[0, 1], [1, 1], [2, 1]], // Второй столбец
      [[0, 2], [1, 2], [2, 2]], // Третий столбец
      [[0, 0], [1, 1], [2, 2]], // Главная диагональ
      [[0, 2], [1, 1], [2, 0]]  // Побочная диагональ
    ];

    // Проверка, находится ли текущая ячейка в одной из выигрышных комбинаций
    return winningCombinations.some(combination => {
      return combination.some(coords => coords[0] === rowIndex && coords[1] === colIndex);
    });
  }
  return false;
},

    checkDraw() {
      let isFilled = true;
      for (let row of this.board) {
        if (row.includes('')) {
          isFilled = false;
          break;
        }
      }
      if (isFilled && !this.winner) {
        this.isDraw = true;
      }
    },
    restartGame() {
      this.board = [
        ['', '', ''],
        ['', '', ''],
        ['', '', '']
      ];
      this.currentPlayer = 'X';
      this.winner = '';
      this.isDraw = false;
    }
  }
};
</script>

<style scoped>
/* Add styling for the game board and cells */
.game-board {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-top: 20px;
}

.row {
  display: flex;
}

.cell {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 50px;
  height: 50px;
  border: 1px solid #ccc;
  cursor: pointer;
  font-size: 24px;
}

.cell-x {
  color: blue;
}

.cell-o {
  color: red;
}

/* Стили для выигрышных линий */
.cell-win::before {
  content: '';
  position: absolute;
  height: 5px; /* Толщина линии */
  background-color: #000; /* Цвет линии */
  z-index: 1; /* Помещаем линию поверх содержимого ячейки */
}

.cell-win.row-0::before,
.cell-win.row-1::before,
.cell-win.row-2::before {
  width: 100%; /* Ширина линии для горизонтальной выигрышной комбинации */
  top: 50%; /* Позиционирование по вертикали */
  left: 0; /* Начало линии от края */
  transform: translateY(-50%);
}

/* Вертикальные линии */
.cell-win.col-0::before,
.cell-win.col-1::before,
.cell-win.col-2::before {
  height: 100%; /* Высота линии для вертикальной выигрышной комбинации */
  top: 0; /* Начало линии от края */
  left: 50%; /* Позиционирование по горизонтали */
  transform: translateX(-50%);
}

/* Диагональные линии */
.cell-win.diagonal-main::before {
  width: 120%; /* Ширина линии для главной диагонали */
  top: 50%; /* Позиционирование по вертикали */
  left: -10%; /* Начало линии от края */
  transform: rotate(45deg) translateY(-50%);
}

.cell-win.diagonal-reverse::before {
  width: 120%; /* Ширина линии для побочной диагонали */
  top: 50%; /* Позиционирование по вертикали */
  right: -10%; /* Начало линии от края */
  transform: rotate(-45deg) translateY(-50%);
}


.status {
  margin-top: 20px;
  text-align: center;
}

button {
  margin-top: 10px;
}
</style>

