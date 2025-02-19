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
        gameData: [],
        quizData: [],
        resultsData: []
      }
    },
    components: {
      Setup, Game, Results
    },
    methods: {
      checkPrevGameExists() {
        const tailVueGameData = localStorage.getItem('tailVueQuiz_gameData');
        if(tailVueGameData) {
          const data = JSON.parse(tailVueGameData);
          this.gameData = data;
          this.quizData = this.gameData.quizData;
          this.isGameRunning = true;
          return true;
        }
        return false;
      },
      handleQuestionsSet(questionsData) {
        this.gameData.quizData = questionsData.results;
        this.isGameRunning = true;
      }
    }
  }
</script>

<template>
  <Setup v-if="!isGameRunning" v-cloak @question-set="handleQuestionsSet" />
  <Game v-if="isGameRunning" :gameData="gameData" v-cloak />
  <Results v-if="isGameEnded" :results="resultsData" />
</template>