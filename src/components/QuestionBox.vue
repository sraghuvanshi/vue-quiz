<template>
    <div class="question-box-container">
      <b-jumbotron>
          <template v-slot:lead>
            {{currentQuestion.question}}
        </template>

        <hr class="my-4">

        <b-list-group>
                <b-list-group-item 
                  v-for="(answer,index) in answers" 
                  v-bind:key="index" 
                  v-on:click="selectAnswer(index)"
                  v-bind:class="answerClass(index)"
                  >
                    {{ answer }}
                </b-list-group-item>
        </b-list-group>

        <b-button variant="primary" 
        v-on:click="submitAnswer"
        v-bind:disabled="selectedIndex === null || answered" 
        >
        Submit
        </b-button>
        <b-button v-on:click="next" variant="success" href="#">Next</b-button>
      </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: [],
      correctIndex: null,
      answered: false
    }
  },
  watch:{
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null
        this.shuffleAnswers()
        this.answered = false
      }
    }
  },
  methods: {
    selectAnswer(index) {
          this.selectedIndex = index
          console.log(this.selectedIndex)
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers) 
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    submitAnswer() {
      let isCorrect = false
      if(this.selectedIndex === this.correctIndex) {
        isCorrect =true
      }
        this.answered = true
        this.increment(isCorrect)
    },
    answerClass(index) {
      let answerClass = ''

      if(!this.answered && this.selectedIndex === index) {
        answerClass = 'selected'
      }else if(this.answered && this.correctIndex === index) {
        answerClass = 'correct'
      }else if(this.answered && this.selectedIndex === index && this.correctIndex !== index) {
        answerClass = 'incorrect'
      }

      return answerClass
    }
  },
  computed: {
    answers() {
    let answers = [...this.currentQuestion.incorrect_answers]
    answers.push(this.currentQuestion.correct_answer)
    return answers
    }
  }
}
</script>

<style scoped>

    .list-group {
      margin-bottom: 20px;
    }

    .list-group-item:hover {
      background-color: #eee;
      cursor: pointer;
    }

    .selected {
      background-color: lightblue;
    }
    .selected:hover {
      background-color: lightblue;
    }
    .correct {
      background-color: lightgreen;
    }
    .incorrect {
      background-color: lightcoral;
    }

    .btn {
      margin: 0 15px;
    }
</style>