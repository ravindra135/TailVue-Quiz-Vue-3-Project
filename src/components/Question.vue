<script>
  export default {
    name: 'Question',
    props: {
      category: {
        type: String,
        required: true
      },
      type: {
        type: String,
        required: true
      },
      question: {
        type: String,
        required: true
      },
      correct_answer: {
        type: String,
        required: true
      },
      incorrect_answers: {
        type: Array,
        required: true
      },
      difficulty: {
        type: String,
        required: false
      }
    },
    data() {
      return {
        options: this.buildOptions()
      }
    },
    methods: {
      submitAnswer(answer) {
        console.log(answer)
      },
      buildOptions() {
        let wrongOptions = this.incorrect_answers
        let correctOption = this.correct_answer
        let optionsArr = [correctOption,...wrongOptions]
        // return this.shuffleOptions(optionsArr)
        return this.shuffleOptions([...optionsArr]);
      },
      shuffleOptions(arr) {
        let shuffled = [...arr];
        for (let i = shuffled.length - 1; i > 0; i--) {
          const j = Math.floor(Math.random() * (i + 1));
          [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
        }
        return shuffled;
      }
     }
  }
</script>

<template>
  <h1 class="mx-auto text-center text-4xl font-bold font-lato my-8" v-html="question"></h1>

  <div class="flex items-center justify-center gap-8 mb-8">
    <div v-for="(option, index) in options" :key="index" @click="submitAnswer(option)" class="border-2 border-gray-300 p-5 rounded-xl cursor-pointer font-noto-sans text-xl font-semibold hover:bg-blue-400 hover:text-white hover:ring-2 hover:ring-blue-300 transition duration-100 ease-in">{{ option }}</div>
  </div>

  <div class="flex items-center justify-between">
    <p class="text-gray-400 text-sm font-noto-sans">Category: <span class="">{{ category }}</span></p>
    <p class="text-gray-400 text-sm"><span class="px-4 py-1 text-white rounded-full font-lato font-noto-sans text-xs" :class="{ 'bg-blue-400': difficulty === 'easy', 'bg-orange-400': difficulty === 'medium', 'bg-red-400': difficulty === 'hard' }">{{ difficulty.toUpperCase() }}</span></p>
  </div>
</template>