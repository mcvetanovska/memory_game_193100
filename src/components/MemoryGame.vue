<template>
  <div class="grid" :style="gridStyle">
    <div
        v-for="card in cards"
        :key="card.id"
        @click="handleCardClick(card)"
    :class="{ 'card': true, 'flipped': card.flipped, 'matched': card.matched }"
    >
    <span v-if="!card.matched">{{ card.emoji }}</span>
  </div>
  </div>
</template>

<script>
export default {
  props: {
    gridSize: Number,
    cards: Array,
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
    };
  },
  methods: {
    handleCardClick(card) {
      if (this.flippedCards.length >= 2 || card.flipped || card.matched) return;

      card.flipped = true;
      this.flippedCards.push(card);

      if (this.flippedCards.length === 2) {
        setTimeout(this.checkMatch, 1000);
      }


      if (!this.isGameStarted) {
        this.startTime = new Date(); // Set the start time
        this.timer = setInterval(this.updatePlayingTime, 1000); // Start the timer
        this.$emit('cardClick'); // Emit the cardClick event
      }

      if (this.areAllCardsMatched()) {
        this.endTime = new Date();
        this.handleGameOver();
      }
    },
    areAllCardsMatched() {
      // Implement your logic to check if all cards are matched
      return this.cards.every((card) => card.matched);
    },
    updatePlayingTime() {
      if (this.startTime && !this.isGameOver) {
        const currentTime = new Date();
        this.playingTime = Math.floor((currentTime - this.startTime) / 1000); // Update playingTime
      }
    },
    checkMatch() {
      if (this.flippedCards[0].emoji === this.flippedCards[1].emoji) {
        this.flippedCards[0].matched = true;
        this.flippedCards[1].matched = true;
      } else {
        this.flippedCards[0].flipped = false;
        this.flippedCards[1].flipped = false;
      }

      this.flippedCards = [];

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

