<template>
  <div class="grid">
    <div
        v-for="card in cards"
        :key="card.id"
        @click="flipCard(card)"
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
  data() {
    return {
      flippedCards: [],
    };
  },
  methods: {
    flipCard(card) {
      if (this.flippedCards.length >= 2 || card.flipped || card.matched) return;

      card.flipped = true;
      this.flippedCards.push(card);

      if (this.flippedCards.length === 2) {
        setTimeout(this.checkMatch, 1000);
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

      // Check for win condition
      if (this.cards.every((card) => card.matched)) {
        this.$emit('gameOver');
      }
    },
  },
};
</script>

<style>
/* Add your CSS styles here if needed */
</style>
