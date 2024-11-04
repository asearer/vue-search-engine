<template>
    <div class="search-results">
      <div v-if="results.length === 0">No results found</div>
      <ul v-else>
        <li v-for="(result, index) in results" :key="index">
          {{ result.title }}
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  export default {
    props: ["results"], // Receives the search results from the parent component
  };
  </script>
  
  <style scoped>
  .search-results ul {
    list-style: none;
    padding: 0;
  }
  .search-results li {
    padding: 0.5em 0;
  }
  </style>
  