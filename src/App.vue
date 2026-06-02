<template>
  <div class="game-container">
    <h1>🐍 Snake Game </h1>

    <div class="score">Score: {{ score }}</div>

    <div
      class="board"
      :style="{ gridTemplateColumns: `repeat(${gridSize}, 20px)` }"
    >
      <div
        v-for="(cell, index) in boardCells"
        :key="index"
        class="cell"
        :class="{
          snake: snake.includes(index),
          food: food === index
        }"
      ></div>
    </div>

    <div class="buttons">
      <button @click="startGame">Start Game</button>
      <button @click="resetGame">Reset</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      gridSize: 20,
      snake: [202, 203, 204],
      direction: 1, // right
      food: 0,
      interval: null,
      score: 0,
    };
  },

  computed: {
    boardCells() {
      return Array(this.gridSize * this.gridSize).fill(0);
    },
  },

  mounted() {
    window.addEventListener("keydown", this.changeDirection);
    this.spawnFood();
  },

  beforeUnmount() {
    window.removeEventListener("keydown", this.changeDirection);
  },

  methods: {
    // ▶ START GAME LOOP
    startGame() {
      if (this.interval) return;

      this.interval = setInterval(() => {
        this.moveSnake();
      }, 150);
    },

    // 🔄 RESET GAME
    resetGame() {
      clearInterval(this.interval);
      this.interval = null;

      this.snake = [202, 203, 204];
      this.direction = 1;
      this.score = 0;

      this.spawnFood();
    },

    // 🐍 MOVE SNAKE
    moveSnake() {
      const head = this.snake[this.snake.length - 1];
      const newHead = head + this.direction;

      // ❌ collision detection
      if (
        newHead < 0 ||
        newHead >= this.gridSize * this.gridSize ||
        this.snake.includes(newHead)
      ) {
        this.resetGame();
        return;
      }

      this.snake.push(newHead);

      // 🍎 eat food
      if (newHead === this.food) {
        this.score++;
        this.spawnFood();
      } else {
        this.snake.shift();
      }
    },

    // 🎮 CONTROLS (WASD + Arrow keys)
    changeDirection(e) {
      const key = e.key.toLowerCase();

      // RIGHT (D / →)
      if ((key === "arrowright" || key === "d") && this.direction !== -1) {
        this.direction = 1;
      }

      // LEFT (A / ←)
      if ((key === "arrowleft" || key === "a") && this.direction !== 1) {
        this.direction = -1;
      }

      // UP (W / ↑)
      if (
        (key === "arrowup" || key === "w") &&
        this.direction !== this.gridSize
      ) {
        this.direction = -this.gridSize;
      }

      // DOWN (S / ↓)
      if (
        (key === "arrowdown" || key === "s") &&
        this.direction !== -this.gridSize
      ) {
        this.direction = this.gridSize;
      }
    },

    // 🍎 SPAWN FOOD (safe position)
    spawnFood() {
      let newFood;

      do {
        newFood = Math.floor(
          Math.random() * this.gridSize * this.gridSize
        );
      } while (this.snake.includes(newFood));

      this.food = newFood;
    },
  },
};
</script>

<style>
.game-container {
  text-align: center;
  font-family: Arial;
}

.score {
  font-size: 20px;
  margin: 10px;
  font-weight: bold;
}

.board {
  display: grid;
  margin: 20px auto;
  width: fit-content;
  border: 2px solid black;
}

.cell {
  width: 20px;
  height: 20px;
  border: 1px solid #eee;
}

.snake {
  background: green;
}

.food {
  background: red;
}

.buttons {
  margin-top: 10px;
}

button {
  margin: 5px;
  padding: 10px;
  cursor: pointer;
}
</style>