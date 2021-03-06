<template>
  <div>
    <b-jumbotron lead="Bootstrap v4 Components for Vue.js 2">
      <template v-slot:lead>{{ currentQuestion.question }}</template>
      <hr class="my-4" />
      <p>List of Questions</p>
      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          v-bind:key="index"
          @click="selectAnswer(index)"
          :class="[!answered && selectedIndex === index ? 'selected' : answered && correctIndex === index ? 'correct-answer' : answered && selectedIndex === index && correctIndex !== index ? 'incorrect' : '']"
        >{{ answer }}</b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="selectedIndex === null || answered"
      >Submit</b-button>
      <b-button variant="success" href="#" @click="next" :disabled="!answered">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    },
    correctAnswer() {
      return this.currentQuestion.correct_answer;
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true; // answered either correct or in correct
      this.increment(isCorrect); // increament count either correct or incorrect
    },
    answerClass(index) {
      let answerClass = "";
      if (!this.answered && this.selectedIndex === index) {
        answerClass = "selected";
      } else if (this.answered && this.correctIndex === index) {
        answerClass = "correct-answer";
      } else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = "incorrect";
      }
      return answerClass;
    }
  }
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}
.list-group-item:hover {
  background-color: #eee;
  cursor: pointer;
}
.btn {
  margin-right: 15px;
}
.selected {
  background-color: lightblue;
}
.correct-answer {
  background-color: green;
}
.incorrect {
  background-color: red;
}
</style>
