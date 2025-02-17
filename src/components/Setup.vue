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
        type: "",
        _token: this.getSessionToken()
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
      getGameCategories() {
        if (this.showGameSetupOptions) {
          const cacheCategories = localStorage.getItem("tailVueQuiz_categories");
          if(cacheCategories) {
            this.categories = JSON.parse(cacheCategories);
          } else {
            try {
              axios.get('https://opentdb.com/api_category.php')
              .then(response => {
                if(response.data.trivia_categories) {
                  localStorage.setItem("tailVueQuiz_categories", JSON.stringify(response.data.trivia_categories))
                  this.categories = response.data.trivia_categories;
                } else {
                  throw new Error("Code 0: Failed to fetch categories");
                }
              }).catch(error => {
                throw new Error("Code 1: Failed to fetch categories");
              });
            } catch (error) {
              this.error = true;
              this.errorMsg = error.message;
            }
          }
        }
      },
      async createGame(event) {
        // disable the button;
        event.target.disabled = true;
        event.target.innerText = "Creating...";
        console.log("Create Game");
        const questionSet = await this.fetchQuestions(this.category, this.difficulty, this.type, this.amount);
        console.log(questionSet);
        if(questionSet) {
          this.$emit('question-set', questionSet)
        } else {
          this.error = true;
          this.errorMsg = "Failed to create game. Please try again later";
          event.target.disabled = false;
          event.target.innerText = "Create Game";
        }
      },
      fetchQuestions(category = "", difficulty = "", type = "", amount = 10) {
        // check token exists;
        if(this._token) {
          let baseUrl = "https://opentdb.com/api.php";
          let queryParams = {
            'amount': amount,
            // 'token': this._token
          }

          if(category) queryParams.category = category;
          if(difficulty) queryParams.difficulty = difficulty;
          if(type) queryParams.type = type;

          const questionResponse = axios.get(baseUrl, {params: queryParams})
            .then(response => {
              return response.data;
            }).catch(error => {
              this.error = true;
              this.errorMsg = "Failed: " + error.message;
              return [];
            });

          return questionResponse;
        } else {
          this.error = true;
          this.errorMsg = "Failed to fetch questions. Please try again later";
          return;
        }
      },
      getSessionToken() {
        const token = localStorage.getItem("tailVueQuiz_token");
        if(token) {
          return token;
        } else {
          // generate new token and set the token;
          try {
            axios.get('https://opentdb.com/api_token.php?command=request')
              .then(response => {
                if(response.data.response_code === 0) {
                  localStorage.setItem("tailVueQuiz_token", response.data.token);
                  return response.data.token;
                } else {
                  throw new Error("Code 3: Failed to generate token");
                }
              }).catch(error => {
                  throw new Error(error.message);
              })
          } catch (error) {
            console.log(error.message)
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
  <div class="w-full h-screen flex justify-center items-center bg-gray-50 relative">
    
    <div v-if="error" class="absolute bottom-18">
      <div class="p-5 rounded-md bg-red-200 text-red-700 mb-4 relative">
          <svg @click="error = !error" class="absolute top-2 right-2 w-5 cursor-pointer transition duration-200" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor"><path fill="none" d="M0 0h24v24H0z"></path><path d="M11.9997 10.5865L16.9495 5.63672L18.3637 7.05093L13.4139 12.0007L18.3637 16.9504L16.9495 18.3646L11.9997 13.4149L7.04996 18.3646L5.63574 16.9504L10.5855 12.0007L5.63574 7.05093L7.04996 5.63672L11.9997 10.5865Z"></path></svg>
          <p>{{ errorMsg }}</p>
      </div>
    </div>

    <!-- Name Block -->
    <div class="p-12 rounded-2xl max-w-lg mx-auto my-auto bg-white shadow-md" v-if="!showGameSetupOptions" v-cloak>
      <img src="@/assets/logo.png" alt="" class="mx-auto mb-8">
      <h2 class="text-center text-xl mb-4 font-bold">Your Daily Quiz Trainer</h2>

      <input v-model="title" type="text" placeholder="Enter Your Name" class="mb-4 border-2 border-gray-300 w-full px-5 py-2 rounded-lg focus:outline-none text-gray-600 font-bold tracking-wider">

      <button @click="submitName" class="bg-red-500 text-white text-center p-3 mx-auto rounded-full cursor-pointer mx-auto flex items-center justify-between hover:shadow-md">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="rgba(255,255,255,1)"><path fill="none" d="M0 0h24v24H0z"></path><path d="M12.1717 12.0005L9.34326 9.17203L10.7575 7.75781L15.0001 12.0005L10.7575 16.2431L9.34326 14.8289L12.1717 12.0005Z"></path></svg>
      </button>
    </div>

    <!-- Game Setup Block -->
    <div class="p-12 rounded-2xl mx-auto my-auto bg-white shadow-md" v-if="showGameSetupOptions" v-cloak>
      <img src="@/assets/logo.png" alt="" class="mx-auto mb-8">
      <h2 class="text-center mb-8 font-lato font-bold text-4xl">Hello 👋 {{ title.trim() }}</h2>
      <div class="flex items-center justify-between mb-4 space-x-8">
        <div class="">
          <label for="category" class="block text-gray-400 text-sm mb-1 font-noto-sans">Select Category</label>
          <select v-model="category" id="category" class="border-2 font-noto-sans border-gray-400 px-5 py-2 w-56 rounded-lg focus:outline-none text-gray-600 font-bold tracking-wider">
            <option value="">Any Category</option>
            <option v-for="(category, index) in categories" :key="index" :value="category.id">{{ category.name }}</option>
          </select>
        </div>
        <div class="">
          <label for="difficulty" class="block text-gray-400 text-sm mb-1 font-noto-sans">Select Difficulty</label>
          <select v-model="difficulty" id="difficulty" class="border-2 border-gray-400 font-noto-sans px-5 py-2 w-56 rounded-lg focus:outline-none text-gray-600 font-bold tracking-wider">
            <option value="">Any Difficulty</option>
            <option value="easy">Easy</option>
            <option value="medium">Medium</option>
            <option value="hard">Hard</option>

          </select>
        </div>
        <div class="">
          <label for="type" class="block text-gray-400 text-sm mb-1 font-noto-sans">Question Type</label>
          <select v-model="type" id="type" class="border-2 border-gray-400 px-5 py-2 font-noto-sans rounded-lg w-56 focus:outline-none text-gray-600 font-bold tracking-wider">
            <option value="">Any Type</option>
            <option value="multiple">Multiple Choice</option>
            <option value="boolean">True/False</option>
          </select>
        </div>
      </div>
      <button @click="createGame" class="px-6 py-3 border-1 border-gray-400 font-source-sans-3 rounded-lg shadow hover:shadow-lg cursor-pointer mx-auto flex items-center justify-center mt-8 hover:border-transparent hover:bg-green-500 hover:text-white transition duration-100 ease-in">Submit</button>
    </div>
  </div>
</template>