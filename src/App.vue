<template>


  <div>

    <ScoreBoard v-bind:winCount="this.winCount" v-bind:loseCount="this.loseCount"/>

    <template v-if="this.question">

      <h1 v-html="this.question"></h1>

      <template v-bind:key="index" v-for="(answer, index) in this.answers">
        <input 
        :disabled="this.answerSubmitted"
        type="radio" 
        name="options" 
        v-bind:value="answer" 
        v-model="this.choosen_answer">
        <label v-html="answer"></label><br>
      </template>

      <button v-if="!this.answerSubmitted" v-on:click="this.submitAnswer()" type="button" class="send">Send</button>

      <section v-if="this.answerSubmitted" class="result">
        <h4 v-if="this.choosen_answer == this.correctAnswer" v-html="'&#9989;  Congratulations, the answer ' + this.correctAnswer + ' is correct.'">
          
        </h4>
        <h4 v-else v-html="'&#10060;  Sorry, you picked the wrong answer. The correct is ' + this.correctAnswer + '.'">
        </h4>
        <button v-on:click="this.getNewQuestion()" class="send" type="button">Next Question</button>
      </section>

    </template>

  </div>

</template>

<script>

import ScoreBoard from "./components/ScoreBoard.vue"

export default {

  name: 'App',
  components: {
    ScoreBoard
  },
  data(){
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      choosen_answer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0,
    }
  },
  methods: {
    submitAnswer() {
      if(!this.choosen_answer){
        console.log("Pick one of the options")
      } else {
        this.answerSubmitted = true;
        if(this.choosen_answer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },
    getNewQuestion(){

      this.answerSubmitted = false;
      this.choosen_answer = undefined;
      this.question = undefined;

      this.axios
      .get("https://opentdb.com/api.php?amount=1&category=20&difficulty=easy")
      .then((response) => {
        this.question = response.data.results[0].question;
        this.incorrectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;
      })
    }, 
  },
  computed: {
    answers() {
      var answers = JSON.parse( JSON.stringify(this.incorrectAnswers) );
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },
  created(){
    this.getNewQuestion();
  }
}

// https://opentdb.com/api.php?amount=1&category=20&difficulty=easy

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;
  
}

h1 {
  margin-top: 40px;
}

#app input[type=radio] {
  margin: 12px 4px;
}

#app .send {
  margin-top: 12px;
  height: 40px;
  min-width: 120px;
  padding: 0 16px;
  color: #fff;
  background-color: #1867c0;
  border: 1px solid #1867c0;
  cursor: pointer;
}

</style>
