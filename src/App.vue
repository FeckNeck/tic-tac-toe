<script setup lang="ts">
import { computed, ref } from "vue";

const grid = ref(Array(9).fill(""));
const player = ref("X");
const historic = ref<any>([]);
let turn = 0;

function play(index: number) {
  if (win.value) return;
  if (grid.value[index] !== "") return;

  if (turn) {
    historic.value.length = turn;
    historic.value.push([...grid.value]);
    turn = 0;
  }

  grid.value[index] = player.value;
  player.value = player.value === "X" ? "O" : "X";
  historic.value.push([...grid.value]);
}

function reset() {
  grid.value = Array(9).fill("");
  player.value = "X";
  historic.value = [];
}

// from react tutorial
const calculateWinner = (squares: any) => {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
};

const win = computed(() => calculateWinner(grid.value.flat()));

function loadTurn(index: number) {
  grid.value = historic.value[index];
  turn = index;
}
</script>

<template>
  <main>
    <h1>Tic tac toe</h1>
    <p>Player turn : {{ player }}</p>
    <div class="container">
      <div class="grid">
        <button
          v-for="(square, index) in grid"
          :key="index"
          class="case"
          @click="play(index)"
          :style="{
            color: grid[index] === 'X' ? '#ef4444' : '#3b82f6',
          }"
        >
          {{ grid[index] }}
        </button>
      </div>
      <div class="historic">
        <button
          v-for="index in historic.length"
          @click="loadTurn(index - 1)"
          class="button"
        >
          turn {{ index }}
        </button>
      </div>
    </div>
    <h1 v-if="win">Player {{ player === "X" ? "O" : "X" }} won !</h1>
    <button @click="reset()" class="button reset">RESET</button>
  </main>
</template>

<style scoped>
.container {
  display: flex;
  align-items: flex-start;
  justify-content: center;
  flex-wrap: wrap;
  gap: 2rem;
}

.grid {
  display: grid;
  grid-auto-rows: 9rem;
  grid-template-columns: repeat(3, 9rem);
}

.case {
  border: 1px solid #1e3851;
  background-color: inherit;
  font-size: xx-large;
  text-align: center;
}

.case:hover {
  background-color: #1e3851;
}

.historic {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  max-height: 27rem;
  flex-wrap: wrap;
}

.button {
  background-color: #1e3851;
  border-radius: 0.25rem;
  border: none;
  color: inherit;
  cursor: pointer;
  font-family: inherit;
  padding: 1rem 3rem;
  transition: background-color 300ms ease;
}

.button:hover {
  background-color: #2dd4bf;
}

.reset {
  margin-block: 1rem;
}
</style>
