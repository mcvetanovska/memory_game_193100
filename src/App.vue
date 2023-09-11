<template>
  <div id="app">
    <h1>Summer Memory Game</h1>
    <DifficultySelector @startGame="startGame" />
    <div v-if="!isGameOver">
      <p>Playing Time: {{ playingTime }} seconds</p>
    </div>
    <MemoryGame :gridSize="gridSize" :cards="cards" @gameOver="handleGameOver" />
    <button @click="restartGame">Restart</button>
  </div>
</template>

<script>
import DifficultySelector from './components/DifficultySelector.vue';
import MemoryGame from './components/MemoryGame.vue';

export default {
  data() {
    return {
      gridSize: 4,
      cards: [],
      isGameOver: false,
      startTime: null,
      endTime: null,
      progress: 0,
    };
  },
  computed: {
    playingTime() {
      if (!this.startTime || !this.endTime) return 0;
      return Math.floor((this.endTime - this.startTime) / 1000);
    },
  },
  methods: {
    startGame(gridSize) {
      this.gridSize = gridSize;
      this.cards = this.generateCards(this.gridSize);
      this.isGameOver = false;
      this.startTime = new Date(); // Record the start time
    },
    generateCards(gridSize) {
      const emojis = ['😎', '🌴', '🏖️', '🌞', '🍦', '⛱️']; // Replace with your image URLs or emoji codes
      const cards = [];
      let emojiIndex = 0;

      for (let i = 0; i < gridSize * gridSize / 2; i++) {
        const emoji = emojis[emojiIndex];
        cards.push({ id: i * 2, emoji, flipped: false, matched: false });
        cards.push({ id: i * 2 + 1, emoji, flipped: false, matched: false });
        emojiIndex = (emojiIndex + 1) % emojis.length; // Cycle through the emojis
      }

      // Shuffle the cards
      for (let i = cards.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [cards[i], cards[j]] = [cards[j], cards[i]];
      }

      return cards;
    },
    handleGameOver() {
      this.endTime = new Date(); // Record the end time
      this.isGameOver = true;
    },
    restartGame() {
      this.cards = [];
      this.progress = 0;
      this.startGame(this.gridSize);
    },
  },
  mounted() {
    this.startGame(this.gridSize);
  },
  components: {
    DifficultySelector,
    MemoryGame,
  },
};
</script>

<style>
/* Add your CSS styles here */
</style>
