<template>
  <div id="app">
    <h1>Coding & Programming Search Engine</h1>
    <div class="search-container" role="search">
      <label for="search-engine" class="sr-only">Choose a search engine:</label>
      <select 
        v-model="selectedEngine" 
        id="search-engine"
        aria-label="Select search engine"
      >
        <option v-for="engine in searchEngines" :key="engine.name" :value="engine.url">
          {{ engine.name }}
        </option>
      </select>

      <div class="keyboard-hint">
        Press <kbd>/</kbd> to focus search | <kbd>Enter</kbd> to search
      </div>
    </div>

    <SearchInput 
      ref="searchInput"
      :value="searchQuery"
      @input="updateSearchQuery"
      @search="performSearch"
    />
    
    <div v-if="isLoading" class="loading">Searching...</div>
    <div v-if="error" class="error">{{ error }}</div>
  </div>
</template>

<script>
import { debounce } from 'lodash';
import SearchInput from "./components/SearchInput.vue";

export default {
  name: 'App',
  components: {
    SearchInput,
  },
  data() {
    return {
      searchQuery: "",
      selectedEngine: "https://docs.python.org/3/search.html?q=",
      searchEngines: [
        // Python
        { name: "Python Docs", url: "https://docs.python.org/3/search.html?q=" },
        // JavaScript
        { name: "MDN JavaScript", url: "https://developer.mozilla.org/en-US/search?q=" },
        { name: "Node.js Docs", url: "https://nodejs.org/api/search.html?q=" },
        // Java
        { name: "Java SE Docs", url: "https://docs.oracle.com/en/java/javase/17/search.html?q=" },
        { name: "Spring Docs", url: "https://docs.spring.io/spring-framework/docs/current/search.html?q=" },
        // C#
        { name: "Microsoft C#", url: "https://learn.microsoft.com/en-us/search/?terms=" },
        // PHP
        { name: "PHP Docs", url: "https://www.php.net/manual-lookup.php?pattern=" },
        // Go
        { name: "Go Docs", url: "https://pkg.go.dev/search?q=" },
        // Ruby
        { name: "Ruby Docs", url: "https://ruby-doc.org/search.html?q=" },
        // Rust
        { name: "Rust Docs", url: "https://doc.rust-lang.org/std/?search=" },
        // Swift
        { name: "Swift Docs", url: "https://developer.apple.com/search/?q=" },
        // Kotlin
        { name: "Kotlin Docs", url: "https://kotlinlang.org/search?q=" },
        // TypeScript
        { name: "TypeScript Docs", url: "https://www.typescriptlang.org/search?q=" },
        // General References
        { name: "DevDocs", url: "https://devdocs.io/#q=" },
        { name: "Dash Docs", url: "dash-plugin://query=" },
      ],
      isLoading: false,
      error: null,
      searchHistory: [],
      maxHistoryItems: 5,
    };
  },
  methods: {
    updateSearchQuery(event) {
      const value = event?.target?.value ?? event;
      this.searchQuery = String(value ?? '');
    },
    trackSearch() {
      if (window?.gtag) {
        window.gtag('event', 'search', {
          search_term: this.searchQuery,
          search_engine: this.selectedEngine
        });
      }
    },
    performSearch() {
      this.error = null;
      this.isLoading = true;
      
      try {
        const query = String(this.searchQuery ?? '').trim();
        if (!query) {
          this.isLoading = false;
          return;
        }

        this.trackSearch();
        const selectedEngine = this.searchEngines.find(e => e.url === this.selectedEngine);
        
        if (!selectedEngine) {
          throw new Error('Invalid search engine selected');
        }

        this.addToHistory({
          query,
          engine: selectedEngine.name,
          timestamp: new Date(),
        });

        const searchUrl = `${this.selectedEngine}${encodeURIComponent(query)}`;
        window.open(searchUrl, "_blank");
      } catch (err) {
        this.error = err.message || "Failed to perform search. Please try again.";
        console.error('Search error:', err);
      } finally {
        this.isLoading = false;
      }
    },
    debouncedSearch: debounce(function() {
      this.performSearch();
    }, 300),
    handleKeydown(event) {
      if (event.key === '/' && !event.target.matches('input, textarea')) {
        event.preventDefault();
        this.$refs.searchInput?.$el.querySelector('input')?.focus();
      }
    },
    addToHistory(searchItem) {
      try {
        this.searchHistory.unshift(searchItem);
        if (this.searchHistory.length > this.maxHistoryItems) {
          this.searchHistory.pop();
        }
        localStorage.setItem('searchHistory', JSON.stringify(this.searchHistory));
      } catch (err) {
        console.error('Failed to save search history:', err);
        // Gracefully fail without breaking the app
      }
    },
  },
  created() {
    try {
      const savedHistory = localStorage.getItem('searchHistory');
      if (savedHistory) {
        this.searchHistory = JSON.parse(savedHistory);
      }
    } catch (err) {
      console.error('Failed to load search history:', err);
      this.searchHistory = []; // Reset to empty array if loading fails
    }
  },
  mounted() {
    window.addEventListener('keydown', this.handleKeydown);
  },
  beforeUnmount() {
    window.removeEventListener('keydown', this.handleKeydown);
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  margin: 2em auto;
  max-width: 800px;
  padding: 0 1em;
}

.search-container {
  display: flex;
  flex-direction: column;
  gap: 1em;
}

select {
  padding: 0.5em;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1em;
}

.keyboard-hint {
  font-size: 0.9em;
  color: #666;
  margin: 0.5em 0;
}

kbd {
  background-color: #f7f7f7;
  border: 1px solid #ccc;
  border-radius: 3px;
  padding: 2px 6px;
  font-size: 0.9em;
}

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  border: 0;
}

.loading {
  color: #666;
  margin: 1em 0;
}

.error {
  color: #ff4444;
  margin: 1em 0;
}

@media (max-width: 600px) {
  #app {
    margin: 1em;
  }
  
  select {
    width: 100%;
  }
}

@media (prefers-color-scheme: dark) {
  body {
    background-color: #1a1a1a;
    color: #fff;
  }

  select {
    background-color: #333;
    color: #fff;
    border-color: #444;
  }

  .keyboard-hint {
    color: #aaa;
  }

  kbd {
    background-color: #444;
    border-color: #666;
    color: #fff;
  }
}
</style>
