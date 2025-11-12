<template>
  <section>
    <div class="form">
      <input v-model="form.title" placeholder="Назва задачі" />
      <input v-model="form.description" placeholder="Опис (необов'язково)" />
      <select v-model="form.priority">
        <option value="low">low</option>
        <option value="medium">medium</option>
        <option value="high">high</option>
      </select>
      <button @click="addTodo">Додати</button>
    </div>

    <TodoFilters
      v-model:filterText="filters.text"
      v-model:filterStatus="filters.status"
      v-model:filterPriority="filters.priority"
      @clear="clearFilters"
    />

    <div class="counters">
      Всього: {{ total }} | Активні: {{ activeCount }} | Виконані: {{ doneCount }}
    </div>

    <ul class="list">
      <TodoItem
        v-for="todo in pagedTodos"
        :key="todo.id"
        :todo="todo"
        @toggle="toggleStatus"
        @delete="deleteTodo"
        @save="saveEdit"
      />
    </ul>

    <div class="pagination" v-if="pages > 1">
      <button @click="prevPage" :disabled="page === 1">◀</button>
      <span>Сторінка {{ page }} / {{ pages }}</span>
      <button @click="nextPage" :disabled="page === pages">▶</button>
    </div>
  </section>
</template>

<script setup>
import { ref, reactive, computed, watch } from 'vue'
import TodoItem from './TodoItem.vue'
import TodoFilters from './TodoFilters.vue'

const TODOS_KEY = 'lab1_todos'

const todos = ref(JSON.parse(localStorage.getItem(TODOS_KEY) || '[]'))

const form = reactive({
  title: '',
  description: '',
  priority: 'low'
})

function addTodo() {
  if (!form.title.trim()) return
  const t = {
    id: Date.now(),
    title: form.title.trim(),
    description: form.description.trim(),
    status: 'active',
    createdAt: new Date().toISOString(),
    priority: form.priority
  }
  todos.value.unshift(t)
  form.title = ''
  form.description = ''
  form.priority = 'low'
  page.value = 1
}

function toggleStatus(todoId) {
  const t = todos.value.find(x => x.id === todoId)
  if (t) t.status = t.status === 'active' ? 'done' : 'active'
}

function deleteTodo(id) {
  todos.value = todos.value.filter(x => x.id !== id)
}

function saveEdit(edited) {
  const idx = todos.value.findIndex(x => x.id === edited.id)
  if (idx !== -1) todos.value.splice(idx, 1, edited)
}

const total = computed(() => todos.value.length)
const activeCount = computed(() => todos.value.filter(t => t.status === 'active').length)
const doneCount = computed(() => todos.value.filter(t => t.status === 'done').length)

const filters = reactive({
  text: '',
  status: 'all',
  priority: 'all'
})

const filtered = computed(() => {
  return todos.value.filter(t => {
    const matchesText =
      !filters.text || [t.title, t.description].join(' ').toLowerCase().includes(filters.text.toLowerCase())
    const matchesStatus = filters.status === 'all' || t.status === filters.status
    const matchesPriority = filters.priority === 'all' || t.priority === filters.priority
    return matchesText && matchesStatus && matchesPriority
  })
})

const perPage = 5
const page = ref(1)
const pages = computed(() => Math.max(1, Math.ceil(filtered.value.length / perPage)))
const pagedTodos = computed(() => {
  const start = (page.value - 1) * perPage
  return filtered.value.slice(start, start + perPage)
})

function prevPage() { if (page.value > 1) page.value-- }
function nextPage() { if (page.value < pages.value) page.value++ }

function clearFilters() {
  filters.text = ''
  filters.status = 'all'
  filters.priority = 'all'
  page.value = 1
}

watch(todos, (n) => {
  localStorage.setItem(TODOS_KEY, JSON.stringify(n))
}, { deep: true })

watch([() => filters.text, () => filters.status, () => filters.priority], () => {
  page.value = 1
})

</script>

<style>
.form { display:flex; gap:8px; margin-bottom:12px; flex-wrap:wrap }
.form input, .form select { padding:6px; border:1px solid #ccc; border-radius:4px }
.form button { padding:6px 8px; border-radius:4px; cursor:pointer }
.counters { margin:10px 0; font-weight:600 }
.list { list-style:none; padding:0; margin:0 }
.pagination { margin-top:12px; display:flex; gap:8px; align-items:center }
</style>
