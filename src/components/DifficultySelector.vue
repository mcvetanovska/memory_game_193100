<template>
  <div>
    <label for="difficulty">Select Difficulty:</label>
    <select id="difficulty" v-model="selectedDifficulty" @change="startGame">
      <option value="4">Easy</option>
      <option value="6">Medium</option>
      <option value="8">Hard</option>
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
      this.$emit('difficultyChange'); // Emit the event when difficulty changes
    },
  },
};
</script>

<style scoped>
label {
  font-size: 18px;
  margin-right: 10px;
  color: #333;
}

select {
  padding: 10px;
  font-size: 16px;
}

</style>

