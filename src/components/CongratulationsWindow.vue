<template>
  <div class="modal-overlay" v-if="showModal">
    <div class="modal">
      <h2>Congratulations!</h2>
      <p>You've won the game!</p>
      <p>Moves: {{ moveCount }}</p>
      <p>Time: {{ formatTime(playingTime-1) }} seconds</p>
      <button @click="restartGame">Play Again</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    showModal: Boolean,
    moveCount: Number,
    playingTime: Number,
  },
  methods: {
    restartGame() {
      this.$emit('restartGame'); // Emit the restartGame event to restart the game
    },
    formatTime(seconds) {
      // Implement a method to format seconds into a time string (e.g., "00:00")
      const minutes = Math.floor(seconds / 60);
      const remainingSeconds = seconds % 60;
      const formattedMinutes = String(minutes).padStart(2, '0');
      const formattedSeconds = String(remainingSeconds).padStart(2, '0');
      return `${formattedMinutes}:${formattedSeconds}`;
    },
  },
  mounted() {
    console.log('CongratulationsWindow mounted with showModal:', this.showModal);
    console.log('isGameWon:', this.isGameWon);
  }
};
</script>


<style scoped>
.modal-overlay {
  /* Styles to create a modal overlay */
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal {
  /* Styles for the modal content */
  background-color: #fff;
  padding: 20px;
  border-radius: 5px;
  text-align: center;
}

button {
  /* Styles for the restart button */
  padding: 10px 20px;
  background-color: #007BFF;
  color: white;
  border: none;
  cursor: pointer;
  font-size: 18px;
  font-weight: bold;
  border-radius: 5px;
}
</style>
