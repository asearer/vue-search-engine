<template>
  <div id="app">
    <h1>DevDocs Hub: Multi-Language Documentation Search</h1>
    <p class="description">Search across official documentation for Python, JavaScript, Java, and many other programming languages - all in one place.</p>
    <div class="search-container" role="search">
      <div class="dropdown-group">
        <div class="select-wrapper">
          <label for="search-engine" class="sr-only">Choose a programming language:</label>
          <select 
            v-model="selectedLanguage" 
            id="search-engine"
            aria-label="Select programming language"
          >
            <option v-for="lang in languages" :key="lang.name" :value="lang.name">
              {{ lang.name }}
            </option>
          </select>
        </div>

        <div class="select-wrapper">
          <label for="framework-docs" class="sr-only">Choose a framework:</label>
          <select
            v-model="selectedEngine"
            id="framework-docs"
            aria-label="Select framework documentation"
          >
            <option v-for="engine in filteredEngines" :key="engine.name" :value="engine.url">
              {{ engine.name }}
            </option>
          </select>
        </div>
      </div>

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
        { name: "Python Docs", url: "https://docs.python.org/3/search.html?q=", language: "Python" },
        { name: "Django", url: "https://docs.djangoproject.com/search/?q=", language: "Python" },
        { name: "Flask", url: "https://flask.palletsprojects.com/search/?q=", language: "Python" },
        { name: "FastAPI", url: "https://fastapi.tiangolo.com/search/?q=", language: "Python" },
        
        // JavaScript
        { name: "MDN JavaScript", url: "https://developer.mozilla.org/en-US/search?q=", language: "JavaScript" },
        { name: "Node.js Docs", url: "https://nodejs.org/api/search.html?q=", language: "JavaScript" },
        { name: "React", url: "https://react.dev/search?q=", language: "JavaScript" },
        { name: "Vue", url: "https://vuejs.org/search/?q=", language: "JavaScript" },
        { name: "Angular", url: "https://angular.io/api?query=", language: "JavaScript" },
        
        // Java
        { name: "Java SE Docs", url: "https://docs.oracle.com/en/java/javase/17/search.html?q=", language: "Java" },
        { name: "Spring Docs", url: "https://docs.spring.io/spring-framework/docs/current/search.html?q=", language: "Java" },
        { name: "Jakarta EE", url: "https://jakarta.ee/search/?q=", language: "Java" },
        
        // C#
        { name: "Microsoft C#", url: "https://learn.microsoft.com/en-us/search/?terms=", language: "C#" },
        { name: ".NET Core", url: "https://learn.microsoft.com/en-us/dotnet/search/?terms=", language: "C#" },
        { name: "ASP.NET", url: "https://learn.microsoft.com/en-us/aspnet/search/?terms=", language: "C#" },
        
        // PHP
        { name: "PHP Docs", url: "https://www.php.net/manual-lookup.php?pattern=", language: "PHP" },
        { name: "Laravel", url: "https://laravel.com/docs/10.x/search?q=", language: "PHP" },
        { name: "Symfony", url: "https://symfony.com/search?q=", language: "PHP" },
        
        // Go
        { name: "Go Docs", url: "https://pkg.go.dev/search?q=", language: "Go" },
        { name: "Go by Example", url: "https://gobyexample.com/search?q=", language: "Go" },
        
        // Ruby
        { name: "Ruby Docs", url: "https://ruby-doc.org/search.html?q=", language: "Ruby" },
        { name: "Ruby on Rails", url: "https://guides.rubyonrails.org/search?q=", language: "Ruby" },
        { name: "Sinatra", url: "https://sinatrarb.com/search?q=", language: "Ruby" },
        
        // Rust
        { name: "Rust Docs", url: "https://doc.rust-lang.org/std/?search=", language: "Rust" },
        { name: "Rust by Example", url: "https://doc.rust-lang.org/rust-by-example/search.html?search=", language: "Rust" },
        
        // Swift
        { name: "Swift Docs", url: "https://developer.apple.com/search/?q=", language: "Swift" },
        { name: "SwiftUI", url: "https://developer.apple.com/documentation/swiftui?q=", language: "Swift" },
        { name: "UIKit", url: "https://developer.apple.com/documentation/uikit?q=", language: "Swift" },
        
        // Kotlin
        { name: "Kotlin Docs", url: "https://kotlinlang.org/search?q=", language: "Kotlin" },
        { name: "Android Developers", url: "https://developer.android.com/s/results?q=", language: "Kotlin" },
        { name: "Ktor", url: "https://ktor.io/search/?q=", language: "Kotlin" },
        
        // TypeScript
        { name: "TypeScript Docs", url: "https://www.typescriptlang.org/search?q=", language: "TypeScript" },
        { name: "Angular", url: "https://angular.io/api?query=", language: "TypeScript" },
        { name: "NestJS", url: "https://docs.nestjs.com/search?q=", language: "TypeScript" },
        
        // General References (these won't be filtered by language)
        { name: "DevDocs", url: "https://devdocs.io/#q=" },
        { name: "Dash Docs", url: "dash-plugin://query=" },
      ],
      isLoading: false,
      error: null,
      searchHistory: [],
      maxHistoryItems: 5,
      selectedLanguage: "Python",
      languages: [
        { name: "Python", frameworks: ["Python Docs", "Django", "Flask", "FastAPI"] },
        { name: "JavaScript", frameworks: ["MDN JavaScript", "Node.js Docs", "React", "Vue", "Angular"] },
        { name: "Java", frameworks: ["Java SE Docs", "Spring Docs", "Jakarta EE"] },
        { name: "C#", frameworks: ["Microsoft C#", ".NET Core", "ASP.NET"] },
        { name: "PHP", frameworks: ["PHP Docs", "Laravel", "Symfony"] },
        { name: "Go", frameworks: ["Go Docs", "Go by Example"] },
        { name: "Ruby", frameworks: ["Ruby Docs", "Ruby on Rails", "Sinatra"] },
        { name: "Rust", frameworks: ["Rust Docs", "Rust by Example"] },
        { name: "Swift", frameworks: ["Swift Docs", "SwiftUI", "UIKit"] },
        { name: "Kotlin", frameworks: ["Kotlin Docs", "Android Developers", "Ktor"] },
        { name: "TypeScript", frameworks: ["TypeScript Docs", "Angular", "NestJS"] },
      ],
    };
  },
  computed: {
    filteredEngines() {
      return this.searchEngines.filter(engine => engine.language === this.selectedLanguage);
    },
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

.description {
  color: #666;
  margin: 0.5em 0 1.5em;
  text-align: center;
  font-size: 1.1em;
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

  .description {
    color: #aaa;
  }
}

.dropdown-group {
  display: flex;
  gap: 1em;
  margin-bottom: 1em;
}

.select-wrapper {
  flex: 1;
}

.select-wrapper select {
  width: 100%;
}

@media (max-width: 600px) {
  .dropdown-group {
    flex-direction: column;
  }
}
</style>
