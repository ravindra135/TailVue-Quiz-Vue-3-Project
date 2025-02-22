<script>
import { errorMessages } from 'vue/compiler-sfc';

  export default {
    name: 'Results',
    data() {
      return {
        title: "Quiz Player",
        error: false,
        errorMsg: "",
        textHeader: "",
        textDesc: ""
      }
    },
    props: {
      results: {
        type: Object,
        required: true,
        default: () => [],
      }
    },
    created() {
      let name = localStorage.getItem("tailVueQuiz_name");
      if(name) {
        this.title = name;
      }

      if(this.results.correctAnswers && this.results.totalQuestions) {

        let wrongAns = this.results.totalQuestions - this.results.correctAnswers;
        let header = "";
        let description = "";
        if(wrongAns == 0) {
          header = "🎯 Perfect Score"
          description = `Wow, ${this.title}! 🎉 You aced it with a perfect score! Share your victory with your friends!`
        } else if (wrongAns == 1 || wrongAns == 2) {
          header = "😊 Almost There";
          description = `Great job, ${this.title}! ✨ Just a couple of slips, but you're almost perfect! Share your score!`
        } else if (wrongAns >= 3 || wrongAns <= 5) {
          header = "😃 Good Effort";
          description = `Nice work, ${this.title}! 👍 You got most of them right! Keep it up and challenge a friend!`
        } else if (wrongAns == 6 || wrongAns == 7) {
          header = "🤔 Keep Practicing";
          description = `Not bad, ${this.title}! 📚 A few more tries, and you'll nail it! Share your score and try again!`
        } else if (wrongAns == 8 || wrongAns == 9) {
          header = "😅 Tough Round";
          description = `That was a tricky one, ${this.title}! 😵 But every challenge is a step toward mastery! Try again and beat your score!`
        } else {
          header = "🤯 Better Luck Next Time";
          description = `Oops, ${this.title}! 😅 Looks like this quiz got the best of you! Don't give up—give it another shot!`
        }
        this.textHeader = header
        this.textDesc = description

      } else {
        this.error = true;
        this.errorMsg = "Failed Create Results"
      }
    },
    methods: {
      shareWithFriends() {
        // TODO: 
      },
      playAgain() {
        // TODO
      }
    }
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

      <h1 class="text-4xl font-lato font-bold text-center">Great! you have done it.</h1>

      <div class="w-full flex justify-center gap-16 mt-8">
        <!-- Marks -->
        <div class="w-48 h-48 rounded-full bg-blue-500 border-8 border-blue-800 text-white flex flex-col items-center justify-center shadow-md">
          <p class="text-4xl animate-bounce" style="font-size: 3.5rem;">{{ this.results.correctAnswers }}</p>
          <hr class="w-16 my-2 border-4 rounded-sm">
          <p class="text-4xl">{{ this.results.totalQuestions }}</p>
        </div>

        <!-- Remarks -->
        <div class="max-w-72 text-right">
          <h3 class="text-2xl font-nato-sans">{{ this.textHeader }}</h3>
          <p class="text-lg font-source-sans-3 !italic mt-4">{{ this.textDesc }}</p>
        </div>
      </div>

      <div class="flex items-center gap-8 mt-12 justify-center">
        <button class="px-5 py-3 rounded-lg bg-green-500 text-white cursor-pointer  font-noto-sans-3 transition duration-150 hover:shadow-md">Share With Your Friends</button>
        <button class="px-5 py-3 rounded-lg bg-transparent border-2 border-gray-500 cursor-pointer font-noto-sans-3 transition duration-200 hover:shadow-md hover:bg-gray-500 hover:border-transparent hover:text-white">Play Again</button>
      </div>
    </div>
  </div>
</template>