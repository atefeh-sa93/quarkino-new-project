<template>
  <div class="container">
    <button v-if="!quizStarted" @click="startQuiz()" class="start-button">Start</button>

    <div v-else class="questions">
      <Quiz/>      
    </div>
  </div>
</template>

<script>
import Quiz from '~/components/Quiz.vue'
export default {
  components: {Quiz},
  data() {
    return {
      quizStarted: false,
    };
  },
  methods: {
    startQuiz() {
      this.quizStarted = true;
    },
  }
};
</script>
<style lang="scss" scoped>
.container {
  display: flex;
  justify-content: center; /* Horizontal centering */
  align-items: center; /* Vertical centering */
  height: 100vh;

  .start-button {
    width: 120px; /* Set width to 120px */
    height: 50px; /* Set height to 50px */
    background-color: #1da1f2; /* Twitter blue color */
    color: white; /* Font color white */
    font-size: 16px; /* Font size 16px */
    font-weight: 600; /* Bold font */
    border: none; /* Remove button border */
    border-radius: 8px;
    cursor: pointer; 
  }
}

</style>
