<template>
  <div id="app">
    <h1>Coding & Programming Search Engine</h1>
    <!-- Dropdown for selecting the search engine -->
    <label for="search-engine">Choose a search engine:</label>
    <select v-model="selectedEngine" id="search-engine">
      <option v-for="engine in searchEngines" :key="engine.name" :value="engine.url">
        {{ engine.name }}
      </option>
    </select>

    <!-- Search input -->
    <SearchInput :onSearch="updateSearchQuery" />
    <!-- Display search results -->
    <SearchResults :results="filteredResults" />
  </div>
</template>

<script>
import SearchInput from "./components/SearchInput.vue";
import SearchResults from "./components/SearchResults.vue";

export default {
  components: {
    SearchInput,
    SearchResults,
  },
  data() {
    return {
      searchQuery: "", // Holds the user's search query
      items: [], // Placeholder for local search results
      selectedEngine: "https://stackoverflow.com/search?q=", // Default search engine
      // List of programming-related search engines
      searchEngines: [
        { name: "Stack Overflow", url: "https://stackoverflow.com/search?q=" },
        { name: "GitHub", url: "https://github.com/search?q=" },
        { name: "MDN Web Docs", url: "https://developer.mozilla.org/en-US/search?q=" },
        { name: "W3Schools", url: "https://www.w3schools.com/search/search.asp?q=" },
        { name: "GeeksforGeeks", url: "https://www.geeksforgeeks.org/search/?q=" },
        { name: "DevDocs", url: "https://devdocs.io/#q=" },
        { name: "Codecademy", url: "https://www.codecademy.com/search?q=" },
        { name: "freeCodeCamp", url: "https://www.freecodecamp.org/search?q=" },
      ],
    };
  },
  computed: {
    filteredResults() {
      // This can still filter items based on local data if necessary
      if (!this.searchQuery) {
        return this.items;
      }
      return this.items.filter((item) =>
        item.title.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
  },
  methods: {
    updateSearchQuery(query) {
      this.searchQuery = query;
    },
    performSearch() {
      // Redirect to the selected search engine with the query
      if (this.searchQuery) {
        const searchUrl = `${this.selectedEngine}${encodeURIComponent(this.searchQuery)}`;
        window.open(searchUrl, "_blank"); // Opens the search in a new tab
      }
    },
  },
  watch: {
    searchQuery(newQuery) {
      if (newQuery) {
        this.performSearch(); // Automatically perform search on query change
      }
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  margin: 2em;
}
</style>
