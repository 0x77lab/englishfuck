<template>
  <div class="quiz">
    <div>
      <md-card md-with-hover>
        <md-ripple>
          <md-card-header>
            <div class="md-title" v-html="question.text"></div>
            <div class="md-subhead">
              Question
              <b>{{ questionNumber }}</b>
            </div>
          </md-card-header>

          <md-card-content>
            <div v-if="question.type === 'tf'">
              <md-radio v-model="answer" value="t">True</md-radio>
              <br>
              <md-radio v-model="answer" value="f">False</md-radio>
              <br>
            </div>
            <div v-if="question.type === 'i'">
              <md-field>
                  <md-input v-model="answer"></md-input>
                </md-field>
            </div>

            <div v-if="question.type === 'mc'">
              <div v-for="(mcanswer,index) in question.answers">
                <md-radio
                  type="radio"
                  :id="'answer'+index"
                  name="currentQuestion"
                  v-model="answer"
                  :value="mcanswer"
                ></md-radio>
                <label :for="'answer'+index">{{mcanswer}}</label>
                <br>
              </div>
            </div>
            
            <div v-if="question.type === 'mci'">
              <div v-for="(mcanswer,index) in question.answers">
                <md-field>
                  <md-input
                  :id="'answer'+index"
                  v-model="answer[index]"
                  :maxlength="mcanswer.length"></md-input>
                </md-field>
                
                <br>
              </div>
            </div>
          </md-card-content>

          <md-card-actions>
            <md-button class="md-primary md-raised" @click="submitAnswer">Answer</md-button>
          </md-card-actions>
        </md-ripple>
      </md-card>
    </div>
  </div>
</template>
<script>
export default {
  name: "question",
  data() {
    if(this.question.type == "mci"){
      return {
      answer: []
    };
    }else{
    return {
      answer: ""
    };
    }
  },
  props: ["question", "question-number"],
  methods: {
    submitAnswer: function() {
      this.$emit("answer", { answer: this.answer });
      this.answer = null;
    }
  }
};
</script>
