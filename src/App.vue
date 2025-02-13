<script>
  import Setup from './components/Setup.vue';
  import Game from './components/Game.vue';
  import Results from './components/Results.vue';

  export default {
    name: 'App',
    data() {
      return {
        isGameRunning: false,
        isGameEnded: false,
        previousGameExists: this.checkPrevGameExists(),
        quizData: [],
        resultsData: []
      }
    },
    components: {
      Setup, Game, Results
    },
    methods: {
      checkPrevGameExists() {
        const tailVueQuizData = localStorage.getItem('tailVueQuiz_quizData');
        if(tailVueQuizData) {
          const data = JSON.parse(tailVueQuizData);
          this.quizData = data;
          return true;
        }

        return false;
      },
    }
  }
</script>

<template>
  <Setup v-if="!isGameRunning" v-cloak />
  <Game v-if="isGameRunning" :quiz-data="quizData" v-cloak />
  <Results v-if="isGameEnded" :results="resultsData" />
</template>