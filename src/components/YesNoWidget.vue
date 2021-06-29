<template>
  <div id="yesNoWidget">
    <h1>Yes or No</h1>
    <p>So write your question</p>
    <input type="text" autofocus v-model="question">
    <p class="answer">{{ answer }}</p>
    <img v-bind:src="answerImage" v-if="answerImage">
  </div>
</template>

<script>
import axios from 'axios';
import loadash from 'lodash';

export default {
  data(){
    return {
      question: "",
      answer: "Waiting for your question ...",
      answerImage: null,
    };
  },

  watch: {
    question: function(){
      this.answer = "Waiting for you to stop typing ...";
      this.answerImage = null;
      this.getDebouncedAnswer();
    }
  },

  created(){
    this.getDebouncedAnswer = loadash.debounce(this.getAnswer, 500);
  },

  methods: {
    getAnswer: function(){
      if(this.question.indexOf('?') === -1){
        this.answer = "Question usually end with ? :)";
        return;
      }

      this.answer = 'thinking';

      var vm = this;
      axios.get('https://yesno.wtf/api')
        .then((response) => {
          vm.answer = response.data.answer;
          vm.answerImage = response.data.image;
        })
        .catch((error) => {
          this.answer = 'Error! we could not fetch the results. '+error;
        })
    },
  }
}
</script>

<style scoped>
  #yesNoWidget{
    max-width: 400px;    
    background-color: #272727;
    border-radius: 10px;
    padding: 15px;
    box-shadow: 0px 3px #797272;
    margin: 15px auto;
    text-align: center;
    font-family: 'Courier New', Courier, monospace;
    color:#ffe000;
    border: 1px solid #000;
  }
  input{
    width: 100%;
    padding: 10px;
    box-sizing: border-box;
    border-radius: 10px;
    border: 1px solid #000;
    background-color: rgb(255 255 255 / 12%);
    color: #fff;
    outline: none;
  }
  .answer{
    font-size: 1.2em;
    font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
    color: #fff;
  }
  img{
    width: 100%;
  }
</style>