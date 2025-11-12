<template>
  <div class="app">
    <h1>Lab1</h1>
    <TodoList />
  </div>
</template>

<script setup>
import TodoList from './components/TodoList.vue'
</script>

<style>
body { font-family: Arial, Helvetica, sans-serif; margin: 20px; background:#f7f7f8; color:#111 }
.app { max-width: 900px; margin: 0 auto; background: #fff; padding: 20px; border-radius: 8px; box-shadow: 0 6px 18px rgba(0,0,0,0.06) }
h1 { font-size: 20px; margin-bottom: 12px }
</style>
