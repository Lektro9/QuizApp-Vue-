<template>
  <div>
    <b-jumbotron>
      <template slot="lead">{{question.frage}}</template>

      <template slot="header">
        {{question.karten_thema}}
      </template>

      <hr class="my-4">

      <b-list-group v-for="(answer, index) in answers" :key="index">
        <b-list-group-item v-html="answer" 
                           @click="chosenAnswer(index)"
                           :class="[!isAnwsered && chosenAnswerIndex === index ? 'selected' :
                            isAnwsered && correctIndex === index ? 'correct' : 
                            isAnwsered && chosenAnswerIndex === index ? 'wrong' : '']"></b-list-group-item>
      </b-list-group>

      <b-button @click="checkAnswer"
                :disabled="chosenAnswerIndex === null || isAnwsered"
       variant="success" href="#">Annehmen</b-button>
      <b-button  variant="primary" @click="next">Nächste Frage</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
export default {
  name: 'QuestionBox',
  props: {
    question: Object, 
    next: Function, //(@click="next")
    answers: Array,
    createAnswers: Function,
    incrementCorrectAnswer: Function,
    incrementWrongAnswer: Function
  },
  data () {
    return {
      chosenAnswerIndex: null,
      isAnwsered: false,
      answerIsCorrect: false,
      correctIndex: null
    }
  },
  methods: {
    chosenAnswer(index) {
      this.chosenAnswerIndex = index
    },
    checkAnswer() {
      this.correctIndex = this.answers.indexOf(this.question.antwort)
      this.isAnwsered = true
      if (this.answers[this.chosenAnswerIndex] === this.question.antwort) {
        this.answerIsCorrect = true
        this.incrementCorrectAnswer()
      } else {
        this.incrementWrongAnswer()
      }
    },
  },
  mounted: function() {
    this.createAnswers()
  },
  watch: {
    question() {
      this.chosenAnswerIndex = null
      this.isAnwsered = false
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.list-group-item:hover {
  background: #EEE;
  cursor: pointer;
}
.btn {
  margin: 10px 1px;
}
.selected {
  background-color: #34bdeb;
}
.correct {
  background-color: #34eb5e;
}
.wrong {
  background-color: red;
}
</style>
