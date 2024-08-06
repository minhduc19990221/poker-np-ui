<template>
    <div>
      <h1>Poker Hand Evaluator</h1>
      <form @submit.prevent="evaluateHand">
        <label for="hand">Enter your hand (e.g., 'H9 H13 H12 H11 H10'):</label>
        <input id="hand" v-model="hand" type="text" required>
        <button type="submit">Evaluate</button>
      </form>
  
      <div v-if="result" class="result">
        <h2>Result:</h2>
        <p>{{ result }}</p>
      </div>
  
      <div v-if="error" class="error">
        <h2>Error:</h2>
        <p>{{ error }}</p>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        hand: '',
        result: null,
        error: null
      }
    },
    methods: {
      async evaluateHand() {
        this.result = null;
        this.error = null;
  
        try {
          const response = await axios.post('http://127.0.0.1:3000/api/v1/hands/evaluate', {
            cards: [this.hand]
          });
  
          if (response.data.result && response.data.result.length > 0) {
            this.result = `${response.data.result[0].hand}`;
          } else if (response.data.error && response.data.error.length > 0) {
            this.error = response.data.error[0].msg;
          } else {
            this.error = 'Unexpected response from server';
          }
        } catch (error) {
          if (error.response && error.response.data && error.response.data.error) {
            this.error = error.response.data.error[0].msg;
          } else {
            this.error = 'An error occurred while evaluating the hand';
          }
          console.error('Error:', error);
        }
      }
    }
  }
  </script>
  
  <style scoped>
  form {
    margin-bottom: 20px;
  }
  label {
    display: block;
    margin-bottom: 5px;
  }
  input {
    width: 300px;
    padding: 5px;
    margin-bottom: 10px;
  }
  button {
    padding: 5px 10px;
  }
  .result, .error {
    margin-top: 20px;
  }
  .error {
    color: red;
  }
  </style>