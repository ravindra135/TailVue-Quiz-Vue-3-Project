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
        gameData: [],
        resultsData: {}
      }
    },
    components: {
      Setup, Game, Results
    },
    methods: {
      checkPrevGameExists() {
        const tailVueGameData = localStorage.getItem('tailVueQuiz_gameData');
        if(tailVueGameData) {
          this.gameData = JSON.parse(tailVueGameData);
          if(this.gameData.currentQuestion < this.gameData.quizData.length) {
            return true; 
          }
          this.gameData = []
        }
        return false;
      },
      handleQuestionsSet(questionsData) {
        this.gameData.quizData = questionsData;
        this.isGameRunning = true;
      },
      gameFinished() {
        let gameData = JSON.parse(localStorage.getItem('tailVueQuiz_gameData'))
        this.resultsData = {
          totalQuestions: gameData.quizData.length,
          correctAnswers: gameData.correctAnswer
        }
        localStorage.removeItem('tailVueQuiz_gameData');
        this.isGameRunning = false;
        this.isGameEnded = true;
      },
      playAgain() {
        this.gameData = [];
        this.isGameEnded = false;
        this.isGameRunning = false;
        this.resultsData = {};
      }
    },
    created() {
      this.isGameRunning = this.checkPrevGameExists();
    }
  }
</script>

<template>
  <Setup v-if="!isGameRunning && !isGameEnded" v-cloak @question-set="handleQuestionsSet" />
  <Game v-if="isGameRunning" :gameData="gameData" v-cloak @game-ended="gameFinished" />
  <Results v-if="isGameEnded" :results="resultsData" @play-again="playAgain" />
</template>