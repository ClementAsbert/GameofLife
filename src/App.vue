<template>
  <div class="buttonContainer">
    <label>Temps en ms :</label>
    <input v-model="intervalSpeed" min="1" type="number" />
    <button @click="start">start</button>
    <button @click="stop">stop</button>
    <button @click="clear">clear</button>
  </div>

  <div class="gridContainer">
    <div v-for="(row, rowIndex) in grid" :key="rowIndex" class="row">
      <div
        v-for="(cell, cellIndex) in row"
        :key="cellIndex"
        class="cell"
        :class="{ alive: cell === 1 }"
        :style="{ width: cellSize + 'px', height: cellSize + 'px' }"
        @click="toggleCell(rowIndex, cellIndex)"
      ></div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue'

const cellSize = 25
let grid = ref([])
let intervalId = null
const intervalSpeed = ref(1000)

onMounted(() => {
  initGrid()
})

function initGrid() {
  const numRow = Math.floor(window.innerHeight / cellSize)
  const numCol = Math.floor(window.innerWidth / cellSize)
  for (let i = 0; i < numRow; i++) {
    const row = []
    for (let j = 0; j < numCol; j++) {
      row.push(0)
    }
    grid.value.push(row)
  }
}

function toggleCell(i, j) {
  this.grid[i][j] = 1 - this.grid[i][j]
}

function start() {
  console.log(intervalSpeed)
  intervalId = setInterval(iteration, intervalSpeed.value)
}

function stop() {
  clearInterval(intervalId)
}

function clear() {
  stop()
  clearGrid()
}

function clearGrid() {
  for (let i = 0; i < grid.value.length; i++) {
    for (let j = 0; j < grid.value[i].length; j++) {
      grid.value[i][j] = 0 // Réinitialise chaque cellule à zéro
    }
  }
}

function iteration() {
  const newGrid = []
  for (let i = 0; i < grid.value.length; i++) {
    newGrid.push([])
    for (let j = 0; j < grid.value[i].length; j++) {
      const neighbors = countNeighbors(i, j)
      if (grid.value[i][j] === 1) {
        newGrid[i][j] = neighbors < 2 || neighbors > 3 ? 0 : 1
      } else {
        newGrid[i][j] = neighbors === 3 ? 1 : 0
      }
    }
  }
  grid.value = newGrid
}

function countNeighbors(x, y) {
  let count = 0
  for (let i = -1; i <= 1; i++) {
    for (let j = -1; j <= 1; j++) {
      if (i === 0 && j === 0) continue
      const newX = x + i
      const newY = y + j
      if (newX >= 0 && newX < grid.value.length && newY >= 0 && newY < grid.value.length) {
        count += grid.value[newX][newY]
      }
    }
  }
  return count
}
</script>

<style scoped>
button {
  size: 30px;
}
.gridContainer {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

.row {
  display: flex;
}

.cell {
  border: 1px solid #ccc;
}

.alive {
  background-color: #fff;
}

.cell:hover {
  transform: scale(1.2);
}
</style>
