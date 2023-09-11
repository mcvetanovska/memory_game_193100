<template>
  <div>
    <label for="difficulty">Select Difficulty:</label>
    <select id="difficulty" v-model="selectedDifficulty" @change="startGame">
      <option value="4">4x4</option>
      <option value="6">6x6</option>
      <option value="8">8x8</option>
    </select>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedDifficulty: '4',
    };
  },
  methods: {
    startGame() {
      this.$emit('startGame', parseInt(this.selectedDifficulty));
    },
  },
};
</script>

<style>
/* Add your CSS styles here if needed */
</style>
