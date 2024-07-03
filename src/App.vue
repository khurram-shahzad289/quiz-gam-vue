<template>

  <div>

    <ScoreCard :winCount="this.winCount" :loseCount="this.loseCount" />

    <template v-if="this.question">
      <h1 v-html="this.question"></h1>

      <template v-for="(answer, index) in this.answers" :key="index">
        <input
        :disabled="this.answerSubmitted" 
        type="radio" 
        name="options" 
        :value="answer" 
        v-model="chossenAnswer"
        >
        <label v-html="answer"></label><br>
      </template>

      <button class="send" type="button" v-if="!this.answerSubmitted" @click="this.submitAnswer()">Send</button>

      <section class="result" v-if="this.answerSubmitted">

        <h4 v-if="this.chossenAnswer == this.correctAnswer">&#9989; congratulation! {{ this.correctAnswer }}  is the correct answer</h4>

        <h4 v-if="this.chossenAnswer != this.correctAnswer">&#10060; I'm sorry, your answer is wrong. The correct answer is {{  this.correctAnswer }}</h4>

        <button class="send" type="button" @click="this.getNewQuestion()">Next Question</button>

      </section>


    </template>





  </div>

</template>

<script>
import ScoreCard from '@/components/ScoreCard.vue'

export default {
  name: 'App',
  components: {
    ScoreCard
  },



  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chossenAnswer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0
    }
  },
  computed: {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },

  methods: {
    submitAnswer() {
      if (!this.chossenAnswer) {
        alert('pick one of the option')
      } else {
        this.answerSubmitted = true;
        if(this.chossenAnswer == this.correctAnswer)
        {
          this.winCount++;
        }
        else
        {
          this.loseCount++;
        }
      }
    },
    getNewQuestion(){

      this.answerSubmitted = false;
      this.chossenAnswer = undefined;
      this.question = undefined

      this.axios
      .get('https://opentdb.com/api.php?amount=1')
      .then((response) => {
        this.question = response.data.results[0].question;
        this.incorrectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;
      })
      .catch((error) => {
        console.error(error);
      });
    }

  },
  created() {
    this.getNewQuestion()
  }

}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;


  input[type=radio] {
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #2c3e50;
    border: 1px solid #1867c0;
    cursor: pointer;
  }



}
</style>
