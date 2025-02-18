<script>
import Question from './Question.vue';

  export default {
    name: 'Game',
    data() {
      return {
        totalQuestions: 0,
        currentQuestion: 1,
        correctAnswer: 0,
        answersData: [],
        error: false,
        errorMsg: ''
      }
    },
    props: {
      quizData: {
        type: Array,
        required: true,
        default: () => [],
      }
    },
    created() {
      if(this.quizData.length === 0) {
        this.error = true
        this.errorMsg = 'No Quiz Data Found'
      } else {
        this.totalQuestions = this.quizData.length
      }
    },
    components: {
      Question
    },
  }
</script>

<template>
  <div class="w-full h-screen flex justify-center items-center bg-gray-50 relative">

    <div v-if="error" class="absolute bottom-18">
      <div class="p-5 rounded-md bg-red-200 text-red-700 mb-4 relative">
          <svg @click="error = !error" class="absolute top-2 right-2 w-5 cursor-pointer transition duration-200" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path fill="none" d="M0 0h24v24H0z"></path><path d="M11.9997 10.5865L16.9495 5.63672L18.3637 7.05093L13.4139 12.0007L18.3637 16.9504L16.9495 18.3646L11.9997 13.4149L7.04996 18.3646L5.63574 16.9504L10.5855 12.0007L5.63574 7.05093L7.04996 5.63672L11.9997 10.5865Z"></path></svg>
          <p>{{ errorMsg }}</p>
      </div>
    </div>

    <div class="absolute top-16">
      <img src="@/assets/logo.png" alt="" class="mx-auto mb-4 mix-blend-multiply">
      <h2 class="text-center text-xl mb-8 font-bold font-noto-sans ml-6">Your Daily Quiz Trainer</h2>
    </div>

    <div class="p-12 rounded-2xl mx-auto my-auto bg-white shadow-md w-full max-w-4xl">

      <p class="text-gray-400 mb-4">Game ID: {{ Math.floor(Math.random() * 10000) }}</p>

      <!-- Questions bar -->
      <div class="w-full">
        <div class="flex w-full items-center justify-between">
          <div v-for="(question, index) in quizData" :key="index" aria-disabled="false" data-step :data-active="(index + 1 === currentQuestion || index + 1 < currentQuestion) ? 'true' : 'false'"  :class="{'group w-full flex items-center': index < totalQuestions - 1}">
            <div class="relative">
              <span class="font-noto-sans relative grid h-10 w-10 place-items-center rounded-full bg-slate-200 group-data-[active=true]:bg-slate-800 group-data-[active=true]:text-white group-data-[completed=true]:bg-slate-800 group-data-[completed=true]:text-white">{{ index + 1 }}</span>
            </div>
            <div v-if="index < totalQuestions - 1" class="flex-1 h-1 bg-slate-200 group-data-[completed=true]:bg-slate-800"></div>
          </div>
        </div>
      </div>

      <!-- Question Block -->
      <div class="w-full mt-8"> 
        <div v-for="(qtn, index) in quizData" :key="'question_block_' + index">
          <Question v-if="(index < totalQuestions - 1) && (currentQuestion === index + 1)" :key="'question_' + index" :category="qtn.category" :question="qtn.question" :type="qtn.type" :correct_answer="qtn.correct_answer" :incorrect_answers="qtn.incorrect_answers" :difficulty="qtn.difficulty"  />
        </div>
      </div>
    </div>
  </div>
</template>