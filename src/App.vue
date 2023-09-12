<template>
  <div id="app"><h1>Memory Game</h1>
    <DifficultySelector @startGame="startGame"/>
    <div v-if="!isGameOver"><p>Playing Time: {{ playingTimeInSeconds }} seconds</p></div>
    <MemoryGame :gridSize="gridSize" :cards="cards" @GameOver="handleGameOver" @cardClick="startTime"/>
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
      isGameStarted: false, // Add a flag to track whether the game has started
      playingTime: 0, // Initialize playingTime
      timer: null,
    };
  },
  computed: {
    playingTimeInSeconds() {
      // Calculate playing time in seconds
      if (!this.startTime) return 0;
      const endTime = this.isGameOver ? this.endTime : new Date();
      return Math.floor((endTime - this.startTime) / 1000);
    },
  },
  methods: {
    startGame(gridSize) {
      this.gridSize = gridSize;
      this.cards = this.generateCards(this.gridSize);
      this.isGameOver = false;
      this.startTime = null; // Record the start time
      this.endTime = null;
      this.isGameStarted = true;
      this.playingTime = 0;
      this.timer = setInterval(this.updatePlayingTime, 1000); // Start a timer to update playing time every second
    },
    handleCardClick() {
      // Handle card clicking logic, including starting the timer
      if (!this.isGameStarted) {
        this.startGame(this.gridSize);
        this.startTime = new Date();
      }

      if (this.areAllCardsMatched()) {
        this.endTime = new Date();
        this.handleGameOver();
      }
    },
    updatePlayingTime() {
      if (this.startTime && !this.isGameOver) {
        const currentTime = new Date();
        this.playingTime = Math.floor((currentTime - this.startTime) / 1000);
      }
    },
    areAllCardsMatched() {
      // Implement your logic to check if all cards are matched
      return this.cards.every((card) => card.matched);
    },
    startTime() {
      if (!this.isGameStarted) {
        this.startGame(this.gridSize);
        this.startTime = new Date();
      }
    },
    generateCards(gridSize) {
      const emojis = ['😎', '🌴', '🏖️', '🌞', '🍦', '⛱️', '👙', '🍨', '🌅', '🌺', '🐕', '🍉', '🍹', '🍭', '📸',
        '🎶', '🩴', '🕶️', '✨', '🎫', '🤿', '🏝️', '⛵', '🛟', '⚓', '🧳', '🍍', '🍒', '🍓', '🥝', '🍇', '🥥', '🐈‍⬛'];
      const cards = [];

      // Shuffle the emojis
      const shuffledEmojis = emojis.slice().sort(() => Math.random() - 0.5);

      for (let i = 0; i < gridSize * gridSize / 2; i++) {
        const emoji = shuffledEmojis[i];
        cards.push({id: i * 2, emoji, flipped: false, matched: false});
        cards.push({id: i * 2 + 1, emoji, flipped: false, matched: false});
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
/* Reset some default styles */
body, ul {
  margin: 0;
  padding: 0;
  list-style: none;
  font-family: Arial, sans-serif;
}

#app {
  text-align: center;
  margin-top: 20px;
}

h1 {
  font-size: 24px;
  margin-bottom: 20px;
  color: #333;
}

button {
  padding: 10px 20px;
  background-color: #4CAF50;
  color: white;
  border: none;
  cursor: pointer;
  margin-top: 20px;
}

button:hover {
  background-color: #45a049;
}
</style>

