<template>
  <div class="time-counter">
    <p>Time: {{ formattedTime }}</p>
  </div>
</template>

<script>
export default {
  props: {
    startTime: Date, // Receive the start time as a prop
    isGameStarted: Boolean, // Receive isGameStarted as a prop
  },
  data() {
    return {
      currentTime: new Date(),
    };
  },
  computed: {
    formattedTime() {
      if (!this.startTime) return '00:00';

      const elapsedTime = this.isGameStarted
          ? this.currentTime - this.startTime
          : 0;
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
  },
  watch: {
    isGameStarted: 'updateCurrentTime',
  },
  mounted() {
    setInterval(this.updateCurrentTime, 1000);
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
