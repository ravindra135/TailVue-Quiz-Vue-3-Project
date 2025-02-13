<script>
  import axios from 'axios';
  export default {
    name: 'Setup',
    data() {
      return {
        title: "",
        errorMsg: "",
        error: false,
        showGameSetupOptions: false,
        categories: [],
        amount: 10,
        category: "",
        difficulty: "",
        type: ""
      }
    },
    watch: {
      showGameSetupOptions(newVal, oldVal) {
        if(newVal && this.showGameSetupOptions) {
          this.getGameCategories();
        }
      }
    },
    methods: {
      submitName() {
        this.error = false;
        // Check if title is not empty
        if(this.title.trim() === "") {
          this.errorMsg = "Please enter your name";
          this.error = true;
        }

        try {
          localStorage.setItem("tailVueQuiz_name", this.title.trim());
          this.showGameSetupOptions = true;
          this.errorMsg = "";
        } catch (error) {
          this.errorMsg = "Failed to save name";
          this.error = true;
        }
      },
      async getGameCategories() {
        if (this.showGameSetupOptions) {
          try {
            const cachedCategories = localStorage.getItem("tailVueQuiz_categories");
            if(cachedCategories) {
              this.categories = JSON.parse(cachedCategories);
            } else {
            const response = await axios.get('https://opentdb.com/api_category.php');
            console.log(response.data);
            localStorage.setItem("tailVueQuiz_categories", JSON.stringify(response.data.trivia_categories))
            this.categories = response.data.trivia_categories;
            }
            // Handle the data as needed
          } catch (error) {
            console.error('Error fetching categories:', error);
            this.errorMsg = 'Failed to fetch game categories';
            this.error = true;
          }
        }
      }
    },
    created() {
      let name =   localStorage.getItem("tailVueQuiz_name");
      if(name) {
        this.title = name;
        this.showGameSetupOptions = true;
      }
    }
  }
  
</script>

<template>
  <div class="w-full h-screen flex justify-center items-center bg-gray-50">
    
    <!-- Name Block -->
    <div class="p-12 rounded-2xl max-w-lg mx-auto my-auto bg-white shadow-md" v-if="!showGameSetupOptions" v-cloak>
      <img src="@/assets/logo.png" alt="" class="mx-auto mb-8">
      <h2 class="text-center text-xl mb-4 font-bold">Your Daily Quiz Trainer</h2>

      <div v-if="error" class="p-5 rounded-md bg-red-200 text-red-700 mb-4 relative">
        <svg @click="error = !error" class="absolute top-2 right-2 w-5 cursor-pointer transition duration-200" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path fill="none" d="M0 0h24v24H0z"></path><path d="M11.9997 10.5865L16.9495 5.63672L18.3637 7.05093L13.4139 12.0007L18.3637 16.9504L16.9495 18.3646L11.9997 13.4149L7.04996 18.3646L5.63574 16.9504L10.5855 12.0007L5.63574 7.05093L7.04996 5.63672L11.9997 10.5865Z"></path></svg>
        <p>{{ errorMsg }}</p>
      </div>

      <input v-model="title" type="text" placeholder="Enter Your Name" class="mb-4 border-2 border-gray-300 w-full px-5 py-2 rounded-lg focus:outline-none text-gray-600 font-bold tracking-wider">

      <button @click="submitName" class="bg-red-500 text-white text-center p-3 mx-auto rounded-full cursor-pointer mx-auto flex items-center justify-between hover:shadow-md">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="rgba(255,255,255,1)"><path fill="none" d="M0 0h24v24H0z"></path><path d="M12.1717 12.0005L9.34326 9.17203L10.7575 7.75781L15.0001 12.0005L10.7575 16.2431L9.34326 14.8289L12.1717 12.0005Z"></path></svg>
      </button>
    </div>

    <!-- Game Setup Block -->
    <div class="p-12 rounded-2xl mx-auto my-auto bg-white shadow-md" v-if="showGameSetupOptions" v-cloak>
      <img src="@/assets/logo.png" alt="" class="mx-auto mb-8">
      <h2 class="text-center mb-8 font-bold text-4xl">Hello 👋 {{ title.trim() }}</h2>
      <div class="flex items-center justify-between mb-4 space-x-8">
        <div class="">
          <label for="category" class="block text-gray-400 text-sm mb-1">Select Category</label>
          <select v-model="category" id="category" class="border-2 border-gray-400 px-5 py-2 w-56 rounded-lg focus:outline-none text-gray-600 font-bold tracking-wider">
            <option value="">Any Category</option>
            <option v-for="(category, index) in categories" :key="index" :value="category.id">{{ category.name }}</option>
          </select>
        </div>
        <div class="">
          <label for="difficulty" class="block text-gray-400 text-sm mb-1">Select Difficulty</label>
          <select v-model="difficulty" id="difficulty" class="border-2 border-gray-400 px-5 py-2 w-56 rounded-lg focus:outline-none text-gray-600 font-bold tracking-wider">
            <option value="">Any Difficulty</option>
            <option value="easy">Easy</option>
            <option value="medium">Medium</option>
            <option value="hard">Hard</option>

          </select>
        </div>
        <div class="">
          <label for="type" class="block text-gray-400 text-sm mb-1">Question Type</label>
          <select v-model="type" id="type" class="border-2 border-gray-400 px-5 py-2 rounded-lg w-56 focus:outline-none text-gray-600 font-bold tracking-wider">
            <option value="">Any Type</option>
            <option value="multiple">Multiple Choice</option>
            <option value="boolean">True/False</option>
          </select>
        </div>
      </div>
      <button class="px-6 py-3 border border-black rounded-lg shadow hover:shadow-lg cursor-pointer mx-auto flex items-center justify-center mt-8 hover:border-transparent hover:bg-red-500 hover:text-white transition duration-100 ease-in">Submit</button>
    </div>
  </div>
</template>