<template>
    <div class="search-input">
      <input
        type="text"
        v-model="query"
        placeholder="Search..."
      />
      <button @click="handleInput">Search</button> <!-- Added Search Button -->
    </div>
  </template>
  
  <script>
  import _ from "lodash"; // Import lodash for debounce functionality
  
  export default {
    props: ["onSearch"], // Accepts a function as a prop
    data() {
      return {
        query: "", // Tracks the input value
      };
    },
    methods: {
      handleInput: _.debounce(function () {
        this.onSearch(this.query);
      }, 300),
    },
  };
  </script>
  
  <style scoped>
  .search-input {
    display: flex;
  }
  .search-input input {
    flex: 1;
    padding: 0.5em;
    font-size: 1em;
  }
  .search-input button {
    padding: 0.5em;
    font-size: 1em;
  }
  </style>
  
  