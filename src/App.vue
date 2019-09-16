<template>
  <div id="app">
    <b-container class="bv-example-row container">
      <b-progress class="mt-2" :max="questions.length" show-value>
          <b-progress-bar :value=correctAnswers variant="success"></b-progress-bar>
          <b-progress-bar :value=wrongAnswers variant="danger"></b-progress-bar>
       </b-progress>
      <b-row>
        <b-col sm="8" offset-sm="2" md="10" offset-md="1">
          <QuestionBox v-if="questions.length"
                       :question="questions[index]"
                       :next="next"
                       :answers="answers"
                       :createAnswers="createAnswers"
                       :incrementCorrectAnswer="incrementCorrectAnswer"
                       :incrementWrongAnswer="incrementWrongAnswer"></QuestionBox>
        </b-col>
      </b-row>
    </b-container>
      <div v-if="index>questions.length-1">
        <!-- <b-button v-b-modal.modal-center>Quiz ist vorbei!</b-button> -->

        <b-modal id="modal-center" centered title="Quiz ist vorbei!">
          <p class="my-4">Vertically centered modal!</p>
        </b-modal>
      </div>
  </div>
</template>

<script>
import QuestionBox from './components/QuestionBox'

export default {
  name: 'App',
  components: {
    QuestionBox
  },
  data() {
    return {
      currentTopic: "",
      questions: [],
      answers: [],
      index: 0,
      correctAnswers: 0,
      wrongAnswers: 0
    }
  },
  methods: {
    next() {
      this.index++
      this.createAnswers()
      this.shuffle()
    },
    createAnswers() {
      this.answers = []
      this.answers.push(this.questions[this.index].antwort)                                       //add correct answer to array
      var randomAnswerNumber = Math.floor(Math.random() * (this.questions.length))
      while (this.answers.length < 4) {
        if (this.answers.includes(this.questions[randomAnswerNumber].antwort)) {                  //check if random answer is already inside
            var randomAnswerNumber = Math.floor(Math.random() * (this.questions.length))          //generate new random index
        }
        else {
            this.answers.push(this.questions[randomAnswerNumber].antwort)
        }
      }
    },
    shuffle() {
      const length = this.answers == null ? 0 : this.answers.length
      if (!length) {
         this.answers = []
      }
      let index = -1
      const lastIndex = length - 1
      const result = this.answers
      while (++index < length) {
        const rand = index + Math.floor(Math.random() * (lastIndex - index + 1))
        const value = result[rand]
        result[rand] = result[index]
        result[index] = value
      }
      this.answers = result
    },
    incrementCorrectAnswer () {
      this.correctAnswers++
    },
    incrementWrongAnswer () {
      this.wrongAnswers++
    }
  },
  mounted: function() {
    this.currentTopic = window.location.pathname.replace('/', '');
    console.log(this.currentTopic)
    fetch('http://127.0.0.1:8000/test/' + this.currentTopic, {
      method: 'get'
    }) 
    .then ((response) => {
      return response.json()
    })
    .then((jsonData) => {
      this.questions = jsonData
      console.log(this.questions)
    })
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.progress {
  margin-bottom: 10px;
}

</style>
