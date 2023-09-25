<template>
  <div>
    <div class="question-box" v-if="currentQuestion">
      <p>{{ currentQuestion.question }}</p>
    </div>
    <div class="counter-box" v-if="currentQuestion">
      <div class="counter-time">
        <p>
          Counter Time:
          <span>{{ timeRemaining }} S</span>
        </p>
      </div>
      <div class="counter-tracker"/>
    </div>
    <div class="answers-box" v-if="currentQuestion">
      <ul>
        <li v-for="(option, optionIndex) in currentQuestion.options" :key="optionIndex">
          <input type="radio" v-model="selectedAnswer" :value="option" />
          <p>{{ option }}</p>
        </li>
      </ul>
    </div>
    <div class="indicator-box" v-if="currentQuestion">
      <div v-for="(option, optionIndex) in questions" :key="optionIndex" :class="['answer-indicator', answerIndicators[optionIndex]]" />
    </div>
    <div v-if="isLastQuestion" class="result-answer">
      <p>You have answered {{ calculateResult() }}% of the questions.</p>
      <button @click="restartQuiz">Restart</button>
    </div>
    
    <button @click="nextQuestion" v-else>Next Question</button>
  </div>
</template>

<script>
export default {
  name: 'Quiz',
  data() {
    return {
      questions: [],
      currentQuestionIndex: 0,
      selectedAnswer: null,
      timeRemaining: 10,
      countdownInterval: null,
      answerIndicators: []
    }
  },
  computed: {
    currentQuestion() {
      return this.questions[this.currentQuestionIndex];
    },
    timerStyle() {
      const maxWidth = 100;
      const width = (this.timeRemaining / 10) * maxWidth;
      return `width: ${width}%`;
    },
    isLastQuestion() {
      console.log('this.currentQuestionIndex', this.currentQuestionIndex)
      console.log('this.questions.length', this.questions.length)
      return this.currentQuestionIndex === this.questions.length;
    },
  },
  async fetch() {
    try {
      const questionsData = require('~/components/questions.json');
      this.questions = questionsData;
    } catch (error) {
      console.error('Error loading questions:', error);
    }
  },
  methods: {
    startTimer() {
      this.countdownInterval = setInterval(() => {
        if (this.timeRemaining > 0) {
          this.timeRemaining--;
        } else {
          this.nextQuestion();
        }
      }, 1000);
    },
    nextQuestion() {
      clearInterval(this.countdownInterval);

      if (this.selectedAnswer !== null) {
        const correctAnswer = this.currentQuestion.correctAnswer;
        const isCorrect = this.selectedAnswer === correctAnswer;

        this.answerIndicators[this.currentQuestionIndex] = isCorrect ? 'green' : 'red';

        this.currentQuestionIndex++;

        this.selectedAnswer = null;
        this.timeRemaining = 10; 
        if (this.isLastQuestion) {
          console.log('4')
          // Display the result for the last question
          this.calculateResult();
        } else {
          this.startTimer();
        }
      }
    },
    calculateResult() {
      const correctAnswers = this.answerIndicators.filter((indicator) => indicator === 'green').length;
      const totalQuestions = this.questions.length;

      console.log('correctAnswers', correctAnswers)
      console.log('totalQuestions', totalQuestions)

      console.log('((correctAnswers / totalQuestions) * 100).toFixed(2)', ((correctAnswers / totalQuestions) * 100).toFixed(2))
      
      return ((correctAnswers / totalQuestions) * 100).toFixed(2);
    },
    restartQuiz() {
      this.currentQuestionIndex = 0;
      this.selectedAnswer = null;
      this.timeRemaining = 10;
      this.answerIndicators = [];
    },
  }
}
</script>
<style lang="scss" scoped>
.question-box {
  background-color: #95d1f5;
  border-radius: 8px;
  text-align: center;
  padding: 20px;
}
.counter-box {
  display: flex;
  flex-direction: column;
  margin-top: 25px;
  position: relative;
  .counter-time {
    text-align: center;
  }
  .counter-tracker {
    background: #0b692f;
    border-radius: 15px;
    padding: 10px;
  }
}
.answers-box {
  ul {
    all: unset;
    list-style: none;
    display: flex;
    flex-direction: column;
    gap: 15px;
    margin-top: 20px;
  }
  li {
    display: flex;
    align-items: baseline;
    justify-content: center;
    gap: 3px;
    background: #d7e5fa;
    border-radius: 12px;
    padding: 10px;
    text-align: center;
  }

}
.indicator-box {
  text-align: center;
  .answer-indicator {
    width: 30px;
    height: 30px;
    border-radius: 50%;
    display: inline-block;
    margin-left: 10px;
    border: 1px solid #333;
    margin-top: 25px;
  }
  .green {
    background-color: green; /* Correct answer indicator */
  }

  .red {
    background-color: red; /* Incorrect answer indicator */
  }
}
.result-answer {
  display: flex;
  flex-direction: column;
  gap: 5px;
  align-items: center;
  > button {
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