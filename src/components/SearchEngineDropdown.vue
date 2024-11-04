<template>
    <div class="search-dropdown">
      <label for="engine">Choose a search engine:</label>
      <select v-model="selectedEngine" @change="emitEngineChange">
        <option v-for="(url, name) in searchEngines" :key="name" :value="url">
          {{ name }}
        </option>
      </select>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        selectedEngine: '',
        searchEngines: {
          "Stack Overflow": "https://stackoverflow.com/search?q=",
          "GitHub": "https://github.com/search?q=",
          "MDN Web Docs": "https://developer.mozilla.org/en-US/search?q=",
          "W3Schools": "https://www.w3schools.com/search/search.asp?q=",
          "GeeksforGeeks": "https://www.geeksforgeeks.org/search/?q=",
          "DevDocs": "https://devdocs.io/#q=",
          "freeCodeCamp": "https://www.freecodecamp.org/search?q=",
          "Codecademy": "https://www.codecademy.com/search?q=",
          "Shodan": "https://www.shodan.io/search?query=",
          "MIT OpenCourseWare": "https://ocw.mit.edu/search/?q=",
          "Coursera": "https://www.coursera.org/search?query=",
          "edX": "https://www.edx.org/search?q=",
          "Kaggle": "https://www.kaggle.com/search?q=",
          "Python Docs": "https://docs.python.org/3/search.html?q=",
          "Khan Academy": "https://www.khanacademy.org/search?page_search_query=",
          "Hackerrank": "https://www.hackerrank.com/domains/tutorials?q=",
          "LeetCode": "https://leetcode.com/problemset/all/?search=",
          "AWS Documentation": "https://docs.aws.amazon.com/search/doc-search.html?searchPath=documentation&searchQuery=",
          "Google Cloud Documentation": "https://cloud.google.com/docs/search?q=",
          "Azure Docs": "https://docs.microsoft.com/en-us/search/?terms=",
          "DigitalOcean Docs": "https://www.digitalocean.com/search?q=",
          "Exploit Database": "https://www.exploit-db.com/search?q=",
          "CVE Details": "https://www.cvedetails.com/google-search-results.php?q=",
          "ElasticSearch Docs": "https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html#q=",
          "Linux Man Pages Online": "https://man7.org/linux/man-pages/dir_section_1.html?search=",
          "Oracle Documentation": "https://docs.oracle.com/en/search.html?q=",
          "MongoDB Docs": "https://docs.mongodb.com/manual/search?q=",
          "PostgreSQL Docs": "https://www.postgresql.org/search/?q=",
          "Docker Documentation": "https://docs.docker.com/search?q=",
          "Ansible Docs": "https://docs.ansible.com/ansible/latest/search.html?q=",
          "Kubernetes Docs": "https://kubernetes.io/docs/search/?q=",
          "Vim Tips Wiki": "https://vim.fandom.com/wiki/Special:Search?query=",
          "Regex101": "https://regex101.com/?q="
        }
      };
    },
    methods: {
      emitEngineChange() {
        this.$emit("engine-change", this.selectedEngine);
      }
    }
  };
  </script>
  
  <style scoped>
  .search-dropdown {
    margin-bottom: 1rem;
  }
  </style>
  