<script setup>
import { ref, computed } from "vue";

const boardSize = 19; // 19x19 grid
const board = ref(Array.from({ length: boardSize }, () => Array(boardSize).fill(null)));
const currentPlayer = ref("black");
const winner = ref(null);

const resetGame = () => {
  board.value = Array.from({ length: boardSize }, () => Array(boardSize).fill(null));
  currentPlayer.value = "black";
  winner.value = null;
};

const placeStone = (row, col) => {
  if (board.value[row][col] || winner.value) return;
  board.value[row][col] = currentPlayer.value;

  if (checkWin(row, col, currentPlayer.value)) {
    winner.value = currentPlayer.value;
  } else {
    currentPlayer.value = currentPlayer.value === "black" ? "white" : "black";
  }
};

const directions = [
  [0, 1], // horizontal
  [1, 0], // vertical
  [1, 1], // diagonal \
  [-1, 1], // diagonal /
];

const checkWin = (row, col, player) => {
  return directions.some(([dx, dy]) => {
    let count = 1;

    for (let i = 1; i <= 4; i++) {
      const r = row + i * dx;
      const c = col + i * dy;
      if (r >= 0 && r < boardSize && c >= 0 && c < boardSize && board.value[r][c] === player) {
        count++;
      } else {
        break;
      }
    }

    for (let i = 1; i <= 4; i++) {
      const r = row - i * dx;
      const c = col - i * dy;
      if (r >= 0 && r < boardSize && c >= 0 && c < boardSize && board.value[r][c] === player) {
        count++;
      } else {
        break;
      }
    }

    return count >= 5;
  });
};

const gameStatus = computed(() =>
  winner.value ? `${winner.value} wins!` : `Current player: ${currentPlayer.value}`
);
</script>

<template>
  <div class="game-container">
    <h1>Renju Game</h1>
    <div class="board-container">
      <!-- Line numbers along the top -->
      <div class="line-numbers top">
        <span v-for="n in boardSize" :key="'top-' + n">{{ n }}</span>
      </div>
      <!-- Main board area -->
      <div class="board">
        <!-- Line numbers along the left -->
        <div class="line-numbers left">
          <span v-for="n in boardSize" :key="'left-' + n">{{ n }}</span>
        </div>
        <!-- Grid with stones -->
        <div class="grid">
          <div
            v-for="(row, rowIndex) in board"
            :key="rowIndex"
            class="row"
          >
            <div
              v-for="(cell, colIndex) in row"
              :key="colIndex"
              class="cell"
              @click="placeStone(rowIndex, colIndex)"
            >
              <div
                v-if="cell"
                :class="['stone', cell]"
              ></div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <p class="status">{{ gameStatus }}</p>
    <button @click="resetGame">Reset Game</button>
  </div>
</template>

<style scoped>
.game-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-family: Arial, sans-serif;
}
.board-container {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.line-numbers {
  display: flex;
  justify-content: center;
  gap: 27px;
  font-size: 14px;
  margin-bottom: 5px;
}
.line-numbers.left {
  flex-direction: column;
  justify-content: flex-start;
  align-items: flex-end;
  margin-right: 5px;
  transform: translateY(6px);
  gap: 7.6px;
}
.line-numbers.top {
  transform: translateX(15px);
  gap: 18px;
}
.board {
  display: flex;
  flex-direction: row;
  align-items: flex-start;
}
.grid {
  display: grid;
  grid-template-rows: repeat(19, 30px);
  grid-template-columns: repeat(19, 30px);
  gap: 0;
  background-color: #f5f5f5;
  border: 2px solid #000;
}
.cell {
  width: 30px;
  height: 30px;
  position: relative;
  cursor: pointer;
}
.cell::before,
.cell::after {
  content: "";
  position: absolute;
  background-color: #000;
}
.cell::before {
  width: 100%;
  height: 1px;
  top: 50%;
  left: 0;
}
.cell::after {
  height: 100%;
  width: 1px;
  left: 50%;
  top: 0;
}
.stone {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 100;
}
.stone.black {
  background-color: black;
}
.stone.white {
  background-color: white;
  border: 1px solid #000;
}
.status {
  margin: 10px 0;
}
button {
  padding: 5px 10px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
button:hover {
  background-color: #0056b3;
}
</style>
