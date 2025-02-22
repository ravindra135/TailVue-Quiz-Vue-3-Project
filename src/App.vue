<script>
  import Setup from './components/Setup.vue';
  import Game from './components/Game.vue';
  import Results from './components/Results.vue';

  export default {
    name: 'App',
    data() {
      return {
        isGameRunning: false,
        isGameEnded: true,
        gameData: [],
        resultsData: {
          totalQuestions: 10,
          correctAnswers: 8
        }
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
          return true;
        }
        return false;
      },
      handleQuestionsSet(questionsData) {
        this.gameData.quizData = questionsData.results;
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
  <Results v-if="isGameEnded" :results="resultsData" />
</template>