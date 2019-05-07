<template>
  <div id="app">
    <div>
      <div v-if="introStage">
        <md-empty-state md-icon="how_to_vote" :md-label="'Welcome to the '+title">
          <md-button class="md-primary md-raised" @click="startQuiz">Start</md-button>
        </md-empty-state>
      </div>

      <div v-if="questionStage">
        <question
          :question="questions[currentQuestion]"
          v-on:answer="handleAnswer"
          :question-number="currentQuestion+1"
        ></question>
      </div>

      <div v-if="resultsStage">
        <md-empty-state md-icon="thumb_up_alt" :md-label="'You finished the '+title">
          You got {{correct}} right out of {{questions.length}} questions. Your percentage is {{perc}}%.
          <md-button class="md-primary md-raised" @click="startQuiz">Start again</md-button>
        </md-empty-state>
      </div>
    </div>
  </div>
</template>

<script>
import question from "./components/question";
import Data from "./quizData";
import low from "lowdb";
import LocalStorage from "lowdb/adapters/LocalStorage";
const adapter = new LocalStorage("englishquiz");
const db = low(adapter);

db.defaults({ lastresult: "" }).write();
window.db = db;
export default {
  name: "App",
  data() {
    return {
      introStage: false,
      questionStage: false,
      resultsStage: false,
      title: "",
      questions: [],
      currentQuestion: 0,
      answers: [],
      correct: 0,
      perc: null,
    };
  },
  created() {
    this.title = Data.title;
    this.questions = Data.questions;
    this.introStage = true;
  },
  methods: {
    startQuiz() {
      this.resultsStage = false;
      this.introStage = false;
      this.questionStage = true;
      console.log(
        "test" + JSON.stringify(this.questions[this.currentQuestion])
      );
    },
    handleAnswer(e) {
      this.answers[this.currentQuestion] = e.answer;
      if (this.currentQuestion + 1 === this.questions.length) {
        this.handleResults();
        this.questionStage = false;
        this.resultsStage = true;
      } else {
        this.currentQuestion++;
      }
    },
    handleResults() {
      console.log("handle results");
      this.questions.forEach((a, index) => {
        window.scomplete = {a: a.answer, aa: this.answers[index]};
        window.scomplete.a.answer["__ob__"] = null;
        window.scomplete.a.answer["__proto__"] = null;
        window.scomplete.aa.answer["__ob__"] = null;
        window.scomplete.aa.answer["__proto__"] = null;
        console.log(this.answers[index]);
        if (window.scomplete.a == window.scomplete.aa){ 
          this.correct++;
        }
      });
      this.perc = ((this.correct / this.questions.length) * 100).toFixed(2);
      console.log(this.correct + " " + this.perc);
      db.get("lastresult")
        .push(
          `You got ${this.correct} right out of ${
            this.questions.length
          } questions. Your percentage is ${this.perc}%.`
        )
        .write();
    }
  },
  components: {
    question
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
