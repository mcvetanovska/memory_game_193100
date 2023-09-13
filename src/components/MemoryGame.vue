<template>

  <div class="grid" :style="gridStyle">
    <div
        v-for="card in cards"
        :key="card.id"
        @click="handleCardClick(card)"
        :class="{ 'card': true, 'flipped': card.flipped, 'matched': card.matched }">
      <span v-if="card.flipped || card.matched">{{ card.emoji }}</span>
    </div>
  </div>
</template>

<script>

export default {
  props: {
    gridSize: Number,
    cards: Array,
    isGameStarted: Boolean, // Receive isGameStarted as a prop
    startTime: Date, // Receive startTime as a prop
  },
  computed: {
    gridStyle() {
      // Calculate the grid columns based on gridSize
      const cardSize = '150px'; // Adjust card size as needed
      const gap = '20px'; // Adjust gap as needed
      const gridColumns = `repeat(${this.gridSize}, ${cardSize})`;

      return {
        gridTemplateColumns: gridColumns,
        gap: gap,
      };
    },
  },
  data() {
    return {
      flippedCards: [],
      timerStartStatus: 'stop',
      processingClick: false, // Add a flag to track click processing
      isGameOver: false,
      };
  },
  methods: {
    handleCardClick(card) {
      if (this.processingClick || card.flipped || card.matched) return;

      card.flipped = true;
      this.flippedCards.push(card);

      if (this.flippedCards.length === 2) {
        this.processingClick = true; // Lock further clicks until processing is done
        setTimeout(() => {
          this.checkMatch();
          this.processingClick = false; // Unlock clicks after processing
          this.$emit('incrementMoveCount');
        }, 1000);
      }

      if (!this.isGameStarted) {
        this.startTime = new Date();
        this.timerStartStatus = 'start';
        this.$emit('cardClick');
      }

      if (this.areAllCardsMatched()) {
        this.endTime = new Date();
        this.handleGameOver();
      }
    },

    checkMatch() {
      console.log('Checking for a match...');
      if (
          this.flippedCards.length === 2 &&
          this.flippedCards[0].emoji === this.flippedCards[1].emoji
      ) {
        this.flippedCards[0].matched = true;
        this.flippedCards[1].matched = true;
        // Emit an event to notify the parent component that a pair is matched
        this.$emit('pairMatched');
      } else {
        this.flippedCards.forEach((card) => (card.flipped = false));
      }

      this.flippedCards = [];
      this.processingClick = false; // Unlock clicks after processing
    },
    handleGameOver() {
      console.log('Checking if all cards are matched...');
      // Check if all cards are matched
      if (this.areAllCardsMatched()) {
        this.endTime = new Date(); // Record the end time
        this.isGameOver = true;
        console.log('handleGameOver is called'); // Add this line
        this.isGameWon = true; // Set isGameWon to true
      }
    },
    areAllCardsMatched() {
      // Check if all cards have been matched
      return this.cards.every((card) => card.matched);
    },
    restartGame() {
      this.cards = [];
      this.timerStartStatus = 'reset'; // Reset timerStarted to false
      this.startGame(this.gridSize);
    },
  },
};
</script>

<style scoped>
.grid {
  display: grid;
  margin: auto;
  justify-content: center;
}

.card {
  width: 150px;
  height: 150px;
  background-color: #007BFF;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  font-size: 50px;
  color: white;
  font-weight: bold;
  border-radius: 5px;
  transition: background-color 0.3s ease-in-out;
}

.card.flipped {
  background-color: #17A2B8;
}

.card.matched {
  background-color: #28A745;
}
</style>
