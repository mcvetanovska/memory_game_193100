<template>
  <div class="time-counter">
    <div>Time: {{ formattedTime }}</div>
  </div>
</template>

<script>
export default {
  props: {
    startTime: Date, // Receive the start time as a prop
    isGameStarted: Boolean, // Receive isGameStarted as a prop
    isGameWon: Boolean, // Receive isGameWon as a prop
  },
  data() {
    return {
      currentTime: new Date(),
    };
  },
  computed: {
    formattedTime() {
      let elapsedTime = 0; // Default value for elapsedTime

      if (this.isGameStarted) {
        elapsedTime = this.currentTime - this.startTime;
      }

      const minutes = Math.floor(elapsedTime / 60000);
      const seconds = Math.floor((elapsedTime % 60000) / 1000);

      return `${this.zeroPrefix(minutes, 2)}:${this.zeroPrefix(seconds, 2)}`;
    },
  },
  methods: {
    zeroPrefix(num, digit) {
      let zero = '';
      for (let i = 0; i < digit; i++) {
        zero += '0';
      }
      return (zero + num).slice(-digit);
    },
    updateCurrentTime() {
      if (this.isGameStarted) {
        this.currentTime = new Date();
      }
    },
    stopCounting() {
      clearInterval(this.timer);
      if (this.isGameWon) {
        this.$emit('gameWon', this.formattedTime); // Emit the gameWon event with the playing time
      }
    },
  },
  watch: {
    isGameStarted: 'updateCurrentTime',
    isGameWon: 'stopCounting', // Watch isGameWon to stop counting when the game is won
  },
  mounted() {
    this.timer = setInterval(() => {
      this.updateCurrentTime();
      if (this.isGameWon) {
        clearInterval(this.timer); // Stop the timer when the game is won
      }
    }, 1000);
  },
};
</script>

<style scoped>
.time-counter {
  text-align: center;
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 20px;
}
</style>
