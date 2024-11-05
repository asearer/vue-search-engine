<template>
  <input
    type="text"
    :value="value"
    @input="$emit('input', $event.target.value)"
    @keyup.enter="$emit('search')"
    placeholder="Search..."
  />
</template>

<script>
export default {
  props: {
    value: {
      type: String,
      default: ''
    }
  }
}
</script>

<style scoped>
.search-input {
  margin: 1em 0;
}

input {
  width: 100%;
  padding: 0.5em;
  font-size: 1.1em;
  border: 1px solid #ddd;
  border-radius: 4px;
}

@media (prefers-color-scheme: dark) {
  input {
    background-color: #333;
    color: #fff;
    border-color: #444;
  }
}
</style>
  
  