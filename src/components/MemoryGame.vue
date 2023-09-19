<template>

  <div class="grid" :style="gridStyle">
    <div
        v-for="card in cards"
        :key="card.id"
        @click="handleCardClick(card)"
        :class="{ 'card': true, 'flipped': card.flipped, 'matched': card.matched }">
      <div class="card-container">
        <div class="card-front"><div class="question-mark">?</div></div>
        <div class="card-back" v-if="card.flipped || card.matched">{{ card.emoji }}</div>
        <div class="check-sign" v-if="card.matched && card.isChecked">✔</div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  props: {
    gridSize: Number,
    cards: Array,
    isGameStarted: Boolean,
    startTime: Date,
  },
  computed: {
    gridStyle() {
      // Calculate the grid columns based on gridSize
      const cardSize = '100px';
      const gap = '15px';
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
      processingClick: false,
      isGameOver: false,
      isGameWon:false,
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

      if (this.areAllCardsMatched() && !this.isGameWon) {
        this.endTime = new Date();
        this.handleGameOver();
        this.isGameWon = true; // Set gameWon to true to prevent further checks
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
        this.flippedCards[0].isChecked = true; // Set isChecked to true for matched cards
        this.flippedCards[1].isChecked = true;
        console.log('Pair matched!');
        // Emit an event to notify the parent component that a pair is matched
        this.$emit('pairMatched');

        console.log('Game won:', this.isGameWon);
      } else {
        this.flippedCards.forEach((card) => (card.flipped = false));
      }

      this.flippedCards = [];
      this.processingClick = false; // Unlock clicks after processing

    },
    handleGameOver() {
      // Delay the check to ensure the last pair of cards updates their 'matched' property
      setTimeout(() => {
        console.log('Checking if all cards are matched...');
        if (this.areAllCardsMatched()) {
          this.endTime = new Date();
          this.isGameOver = true;
          console.log('Game won:', true);
          this.isGameWon = true;
        }
      }, 200);
    },
    areAllCardsMatched() {
      console.log('Checking if all cards are matched...');
      const allMatched = this.cards.every((card) => card.matched);
      console.log('All cards matched:', allMatched);
      return allMatched;
    },
    restartGame() {
      this.cards = [];
      this.flippedCards = [];
      this.processingClick = false;
      this.isGameOver = false;
      this.isGameWon = false;
      this.timerStartStatus = 'reset';
      this.startTime=null;

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
  perspective: 1000px;
}

.card {
  width: 100px;
  height: 100px;
  position: relative;
  perspective: 1000px;
  cursor: pointer;
  transform-style: preserve-3d;
  transition: transform 0.5s;
}

.card .card-container {
  width: 100%;
  height: 100%;
  transition: transform 0.5s ease-in-out;
  transform-style: preserve-3d;
}

.card.flipped .card-container {
  transform: rotateY(180deg);
}

.card-back {
  border: 3px solid #007BFF;
  border-radius: 10px;
  transform: rotateY(180deg);
}


.card .card-container .card-front,
.card .card-container .card-back {
  width: 100%;
  height: 100%;
  border-radius: 10px;
  position: absolute;
  backface-visibility: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 50px;
  font-weight: bold;
}

.card .card-container .card-front {
  background-color: #007BFF;
  color: white;
}

.card .card-container .card-back {
  background-color: white;
  border: 3px solid #007BFF;
  color: #007BFF;
}

.card:hover {
  transform: scale(1.05);
}

.check-sign {
  position: absolute;
  bottom: 5px;
  left: 10px;
  font-size: 30px;
  color: #28a745;
  transform: rotateY(180deg);
}

.question-mark {
  font-size: 30px;
  color: white;
}
</style>
