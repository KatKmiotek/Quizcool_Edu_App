<template lang="html">

  <div>

    <audio id="correct">
      <source src="../assets/sfx/correct.wav" type="audio/wav">
        </audio>

        <audio id="incorrect">
          <source src="../assets/sfx/incorrect.wav" type="audio/wav">
            </audio>

        <p>{{escapeHtml(question.question)}}</p>
        <div class="main-font" v-if="!this.answer">
          <label for="answer">Answer:</label>
          <div v-for="i in answers">
            <input v-on:change="sendScore()" v-model="answer" type="radio" name="answer" :value="i"> {{ i }}</input>
            <br>
          </div>
        </div>
        <br>
    <div>
      <img class="correct" v-if="this.answer === question.correct_answer" src="../assets/feedback/correct.png">
      <img class="incorrect" v-if="this.answer !== question.correct_answer && this.answer" src="../assets/feedback/incorrect.png">
      <br>
    </div>

  </div>
</template>

<script>
import { eventBus } from '../main.js'



export default {
  name: 'question-item',
  props: ['question'],
  data(){
    return {
      answer: null,
      userScore: 0,
      answers: []    }
  },
  computed: {

  },
  methods: {
    sendScore() {

      if (this.answer === this.question.correct_answer){
        this.userScore += 1

        let audio = document.getElementById("correct");
        this.playAudio(audio);
        eventBus.$emit('send-score', this.userScore)
      }

      if (this.answer !== this.question.correct_answer){

        let audio = document.getElementById("incorrect");
        this.playAudio(audio);

        eventBus.$emit('send-score', this.userScore)
      }
      // this.sleep(3000)
    },
    sleep: function(milliseconds) {
      const date = Date.now();
      let currentDate = null;
      do {
        currentDate = Date.now();
      } while (currentDate - date < milliseconds);
    },

    escapeHtml(question) {
      return question
      .replace(/&amp/g, " ")
      .replace(/&lt;/g, "<")
      .replace(/&gt;/g, ">")
      .replace(/&quot;/g, "")
      .replace(/&#039;/g, "'")
      .replace(/&deg;/g, "°")
      // .replace(/&#039;{str}&#039;/g,"{str}")
    },

    getAnswers(){
      this.question.incorrect_answers.map((incorrectAnswer) => {
        this.answers.push(incorrectAnswer)
      })
      this.answers.push(this.question.correct_answer);
      return this.answers
    },

    shuffleAnswers(array){
      for (let i = array.length - 1; i > 0; i--) {
        let j = Math.floor(Math.random() * (i + 1));
        let temp = array[i];
        array[i] = array[j];
        array[j] = temp;
      }
    },
    playAudio(audio) {
      audio.pause();
      audio.currentTime = 0;
      audio.play();
    }
    // pauseAudio(audio) {
    //   this.audio.pause();
    // }
  },
  mounted() {
    eventBus.$on('category-selected', (category) => {
      this.answer = null})

      this.getAnswers();
      this.shuffleAnswers(this.answers);
    },

  }
  </script>





  <style lang="css" scoped>


img {
  width: 175px;
  margin: 10px;
  padding-bottom: 10px;
  text-align:center;
}

input {
  font-weight: 100
}

</style>
