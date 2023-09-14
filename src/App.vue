<template>
  <div id="app">
    <h1 class="app-title">Memory Game</h1>
    <DifficultySelector @startGame="startGame"/>
    <TimeCounter
        :startTime="startTime"
        :isGameStarted="isGameStarted"
        :isGameWon="isGameWon"
    />
    <div class="move-counter">
      <p>Moves: {{ moveCount }}</p>
    </div>
    <ProgressBar
        :totalPairs="totalPairs"
        :matchedPairs="matchedPairs"
        :progress="progress"
    />
    <button class="restart-button" @click="restartGame">Restart</button>
    <MemoryGame
        :gridSize="gridSize"
        :cards="cards"
        :isGameStarted="isGameStarted"
        :startTime="startTime"
        @GameOver="handleGameOver"
        @cardClick="startTimer"
        @incrementMoveCount="incrementMoveCount"
        @pairMatched="incrementMatchedPairs"
    />
    <CongratulationsWindow
        :showModal="isGameWon"
        :moveCount="moveCount"
        :playingTime="playingTime"
        @restartGame="restartGame"

    />
  </div>
</template>

<script>
import DifficultySelector from './components/DifficultySelector.vue';
import MemoryGame from './components/MemoryGame.vue';
import TimeCounter from "./components/TimeCounter.vue";
import ProgressBar from "./components/ProgressBar.vue";
import CongratulationsWindow from "./components/CongratulationsWindow.vue";

export default {
  data() {
    return {
      gridSize: 4,
      cards: [],
      isGameOver: false,
      startTime: null,
      endTime: null,
      isGameStarted: false,
      moveCount: 0,
      timer: null,
      playingTime: 0,
      totalPairs: 0, // Initialize totalPairs
      matchedPairs: 0, // Initialize matchedPairs
      isGameWon: false, // Add a data property to track game win
      progress: 0,
    };
  },
  methods: {
    startGame(gridSize) {
      this.gridSize = gridSize;
      this.cards = this.generateCards(this.gridSize);
      this.isGameOver = false;
      this.startTime = new Date();
      this.endTime = null;
      this.isGameStarted = true;
      this.stopTimer();
      this.timer = setInterval(this.updatePlayingTime, 1000);
      this.moveCount = 0;
      this.matchedPairs = 0;
      this.totalPairs = this.gridSize * this.gridSize / 2;
      this.isGameWon= false;
      this.restartGame();
    },
    startTimer() {
      if (!this.isGameStarted) {
        this.startGame(this.gridSize);
        this.isGameStarted = true; // Indicate that the game has started
        this.startTime = new Date();
      }
    },
    stopTimer() {
      if(this.isGameWon) {
        clearInterval(this.timer);
        this.timer = null;
      }
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
    incrementMoveCount() {
      this.moveCount += 1; // Increment moveCount in the parent component
    },
    incrementMatchedPairs() {
      this.matchedPairs += 1;
      this.updateProgress(); // Call the method to update the progress

      if (this.matchedPairs === this.totalPairs && !this.isGameWon) {
        this.endTime = new Date();
        this.handleGameOver();
        this.isGameWon = true; // Set isGameWon to true to prevent further checks
      }
    },
    updateProgress() { // Implement this method to calculate progress
      if (this.totalPairs === 0) {
        this.progress = 0;
      } else {
        this.progress = (this.matchedPairs / this.totalPairs) * 100;
      }
    },
    areAllCardsMatched() {
      console.log('Checking if all cards are matched...');
      const allMatched = this.cards.every((card) => card.matched);
      console.log('All cards matched:', allMatched);
      return allMatched;
    },
    updatePlayingTime() {
      if (this.startTime) {
        const currentTime = this.isGameOver ? this.endTime : new Date();
        this.playingTime = Math.floor((currentTime - this.startTime) / 1000);
      } else {
        this.playingTime = 0; // Set to 00:00 when not playing
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
        cards.push({id: i * 2, emoji, flipped: false, matched: false, isChecked: false});
        cards.push({id: i * 2 + 1, emoji, flipped: false, matched: false, isChecked: false});
      }

      // Shuffle the cards
      for (let i = cards.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [cards[i], cards[j]] = [cards[j], cards[i]];
      }

      return cards;
    },
    handleGameOver() {
      // Check if all cards are matched
      if (this.areAllCardsMatched()) {
        this.endTime = new Date(); // Record the end time
        this.isGameOver = true;
        console.log('handleGameOver is called'); // Add this line
        this.isGameWon = true; // Set isGameWon to true

      }
    },
    restartGame() {
      this.cards = [];
      this.isGameStarted = false; // Stop the game
      this.playingTime = 0;
      this.moveCount = 0;
      this.matchedPairs = 0;
      this.startTime = null; // Reset start time
      this.isGameWon = false; // Reset isGameWon
      this.startGame(this.gridSize); // Start a new game
    },
  },
  mounted() {
    this.startGame(this.gridSize);
  },
  components: {
    ProgressBar,
    TimeCounter,
    DifficultySelector,
    MemoryGame,
    CongratulationsWindow,
  },
};
</script>

<style>
/* Reset some default styles */
body,
ul {
  margin: 0;
  padding: 0;
  list-style: none;
  font-family: Arial, sans-serif;
}

#app {
  text-align: center;
  margin-top: 20px;
}

.app-title {
  font-size: 36px;
  margin-bottom: 20px;
  color: #007BFF;
  text-transform: uppercase;
}

.restart-button {
  padding: 10px 20px;
  background-color: #007BFF;
  color: white;
  border: none;
  cursor: pointer;
  font-size: 18px;
  font-weight: bold;
  border-radius: 5px;
  transition: background-color 0.3s ease-in-out;
}

.restart-button:hover {
  background-color: #45a049;
}

.move-counter {
  margin-top: 20px;
}


button {
  padding: 10px 20px;
  background-color: #007BFF;
  color: white;
  border: none;
  cursor: pointer;
  font-size: 18px;
  font-weight: bold;
  border-radius: 5px;
}

button:hover {
  background-color: #45a049;
}
</style>

