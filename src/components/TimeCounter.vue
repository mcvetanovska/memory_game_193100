<template>
  <div class="time-counter">
    <div>Time: {{ formattedTime }}</div>
  </div>
</template>

<script>
export default {
  props: {
    startTime: Date,
    isGameStarted: Boolean,
    isGameWon: Boolean,
  },
  data() {
    return {
      currentTime: new Date(),
      timer: null,
    };
  },
  computed: {
    formattedTime() {
      let elapsedTime = 0;

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
        this.$emit('gameWon', this.formattedTime);
      }
    },
  },
  watch: {
    isGameStarted: 'updateCurrentTime',
    isGameWon: 'stopCounting',
    startTime() {
      // Reset the timer when startTime changes (game restart)
      clearInterval(this.timer);
      this.currentTime = new Date();
      if (this.isGameStarted) {
        this.timer = setInterval(() => {
          this.updateCurrentTime();
          if (this.isGameWon) {
            clearInterval(this.timer);
          }
        }, 1000);
      }
    },
  },
  mounted() {
    // Start the timer on component mount if the game is already started
    if (this.isGameStarted) {
      this.timer = setInterval(() => {
        this.updateCurrentTime();
        if (this.isGameWon) {
          clearInterval(this.timer);
        }
      }, 1000);
    }
  },
  beforeUnmount() {
    clearInterval(this.timer);
  },
};
</script>

<style scoped>

</style>
