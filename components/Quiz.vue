<template>
  <div>
    <button v-if="!quizStarted" @click="startQuiz()" class="start-button">
      Start
    </button>
    <div v-else>
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
        <div class="counter-wrapper">
          <div class="counter-tracker" :style="timerStyle" :class="{ 'countdown-finished': timeRemaining === 0 }"></div>
        </div>
      </div>
      <div class="answers-box" v-if="currentQuestion">
        <div
          v-for="(option, optionIndex) in currentQuestion.options"
          :key="optionIndex"
          @click="selectAnswer(option)"
          :class="[
            'answer-option',
            { 'selected-answer': option === selectedAnswer },
          ]"
        >
          {{ option }}
        </div>
      </div>
      <div class="indicator-box" v-if="currentQuestion">
        <div
          v-for="(option, optionIndex) in questions"
          :key="optionIndex"
          :class="['answer-indicator', answerIndicators[optionIndex]]"
        />
      </div>
    </div>
    <div v-if="isLastQuestion && quizStarted" class="result-answer">
      <p>You have answered {{ calculateResult() }}% of the questions.</p>
      <button @click="restartQuiz">Restart</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Quiz",
  data() {
    return {
      questions: [],
      currentQuestionIndex: 0,
      selectedAnswer: null,
      timeRemaining: 10,
      countdownInterval: null,
      countdownFinished: false,
      answerIndicators: [],
      quizStarted: false,
    };
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
      return this.currentQuestionIndex === this.questions.length;
    },
  },
  async fetch() {
    try {
      const questionsData = require("~/data/questions.json");
      this.questions = questionsData;
    } catch (error) {
      console.error("Error loading questions:", error);
    }
  },
  methods: {
    startQuiz() {
      this.quizStarted = true;
      this.startTimer();
    },
    startTimer() {
      this.countdownInterval = setInterval(() => {
        if (this.timeRemaining > 0) {
          this.timeRemaining--;
        } else {
          clearInterval(this.countdownInterval);
          this.countdownFinished = true;
          this.selectAnswer();
        }
      }, 1000);
      this.timeRemaining = 10;
    },
    selectAnswer(selectedOption) {
      clearInterval(this.countdownInterval);

      if (this.selectedAnswer === null && this.currentQuestion) {
        this.selectedAnswer = selectedOption;

        if (this.selectedAnswer !== null) {
          const correctAnswer = this.currentQuestion.correctAnswer;
          const isCorrect = this.selectedAnswer === correctAnswer;

          this.answerIndicators[this.currentQuestionIndex] = isCorrect
            ? "green"
            : "red";

          setTimeout(() => {
            this.currentQuestionIndex++;
            this.selectedAnswer = null;

            if (!this.isLastQuestion) {
              this.startTimer();
            } else {
              this.calculateResult();
            }
          }, 1000);
        }
      }
    },

    calculateResult() {
      const correctAnswers = this.answerIndicators.filter(
        (indicator) => indicator === "green"
      ).length;
      const totalQuestions = this.questions.length;
      return (correctAnswers / totalQuestions) * 100;
    },
    restartQuiz() {
      this.currentQuestionIndex = 0;
      this.selectedAnswer = null;
      this.timeRemaining = 10;
      this.answerIndicators = [];
      this.quizStarted = false;
    },
  },
};
</script>

<style lang="scss" scoped>
.start-button {
  width: 120px;
  height: 50px;
  background-color: #1da1f2;
  color: white;
  font-size: 16px;
  font-weight: 600;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}
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

  .counter-time {
    text-align: center;
  }

  .counter-wrapper {
    background: rgb(230, 225, 225); /* Initial gray background color */
    border-radius: 15px;
    overflow: hidden;

    .counter-tracker {
      background: #0b692f;
      padding: 10px;
      transition: width 1s ease, background 1s ease; 
    }
    .countdown-finished {
      background: red; /* Change the background color when countdown is finished */
      width: 0; /* Reduce width to 0 */
      transition: background 1s ease, width 1s ease; /* Add transitions for background and width */
    }
  }
  
}
.answers-box {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-top: 20px;
  .answer-option {
    background: #d7e5fa;
    border-radius: 12px;
    padding: 10px;
    text-align: center;
    cursor: pointer;
  }
  .selected-answer {
    background: #ff9800;
    color: #fff;
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
    background-color: green;
  }
  .red {
    background-color: red;
  }
}
.result-answer {
  display: flex;
  flex-direction: column;
  gap: 5px;
  align-items: center;
  > button {
    width: 120px;
    height: 50px;
    background-color: #1da1f2;
    color: white;
    font-size: 16px;
    font-weight: 600;
    border: none;
    border-radius: 8px;
    cursor: pointer;
  }
}
</style>
