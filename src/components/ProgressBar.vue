<template>
  <div class="progress-bar">
    <div class="progress-bar-fill" :style="progressStyle"></div>
  </div>
</template>

<script>
export default {
  props: {
    totalPairs: Number,
    matchedPairs: Number,
    progress: Number,
  },
  computed: {
    progressStyle() {
      const percentage = (this.matchedPairs / this.totalPairs) * 100 + '%';
      return { width: percentage };
    },
  },
};
</script>

<style scoped>
.progress-bar {
  width: 50%;
  max-width: 300px;
  height: 15px;
  background-color: #ccc;
  border-radius: 5px;
  margin: 20px auto;
  position: relative;
}

.progress-bar-fill {
  height: 100%;
  background-color: #007BFF;
  border-radius: 5px;
  transition: width 0.3s ease-in-out;
}
</style>
