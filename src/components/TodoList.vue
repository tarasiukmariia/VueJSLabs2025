<template>
  <div class="todo-container">
    <h1>To-Do List</h1>

    <div class="add-task">
      <input v-model="newTask.title" placeholder="Назва" />
      <input v-model="newTask.description" placeholder="Опис" />
      <select v-model="newTask.priority">
        <option disabled value="">Пріоритет</option>
        <option value="low">Низький</option>
        <option value="medium">Середній</option>
        <option value="high">Високий</option>
      </select>
      <button @click="addTask">Додати</button>
    </div>

    <TaskFilters
        v-model:filters="filters"
        @clear="clearFilters"
    />

    <div class="counters">
      <p>Всього: {{ totalTasks }}</p>
      <p>Активні: {{ activeTasks }}</p>
      <p>Виконані: {{ completedTasks }}</p>
    </div>

    <div v-if="paginatedTasks.length">
      <ul class="task-list">
        <TaskItem
            v-for="task in paginatedTasks"
            :key="task.id"
            :task="task"
            @toggle="toggleStatus"
            @delete="deleteTask"
            @edit="editTask"
        />
      </ul>
    </div>
    <p v-else>Немає завдань</p>

    <div class="pagination" v-if="filteredTasks.length > tasksPerPage">
      <button @click="prevPage" :disabled="currentPage === 1">Назад</button>
      <span>Сторінка {{ currentPage }} з {{ totalPages }}</span>
      <button @click="nextPage" :disabled="currentPage === totalPages">Вперед</button>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed, watch } from 'vue'
import TaskItem from './TaskItem.vue'
import TaskFilters from './TaskFilters.vue'

const newTask = reactive({ title: '', description: '', priority: '' })
const tasks = ref(JSON.parse(localStorage.getItem('tasks')) || [])
const filters = reactive({ title: '', description: '', status: '', priority: '', fromDate: '', toDate: '' })
const currentPage = ref(1)
const tasksPerPage = 5

const filteredTasks = computed(() => {
  return tasks.value.filter(task => {
    const titleMatch = task.title.toLowerCase().includes(filters.title.toLowerCase())
    const descMatch = task.description.toLowerCase().includes(filters.description.toLowerCase())
    const statusMatch = !filters.status || task.status === filters.status
    const priorityMatch = !filters.priority || task.priority === filters.priority
    const date = new Date(task.createdAt)
    const from = filters.fromDate ? new Date(filters.fromDate) : null
    const to = filters.toDate ? new Date(filters.toDate) : null
    const dateMatch = (!from || date >= from) && (!to || date <= to)
    return titleMatch && descMatch && statusMatch && priorityMatch && dateMatch
  })
})

const totalTasks = computed(() => tasks.value.length)
const activeTasks = computed(() => tasks.value.filter(t => t.status === 'active').length)
const completedTasks = computed(() => tasks.value.filter(t => t.status === 'done').length)
const totalPages = computed(() => Math.ceil(filteredTasks.value.length / tasksPerPage))
const paginatedTasks = computed(() => {
  const start = (currentPage.value - 1) * tasksPerPage
  return filteredTasks.value.slice(start, start + tasksPerPage)
})

watch(tasks, newVal => {
  localStorage.setItem('tasks', JSON.stringify(newVal))
}, { deep: true })

function addTask() {
  if (!newTask.title.trim()) return alert('Введіть назву завдання!')
  const task = {
    id: Date.now(),
    title: newTask.title,
    description: newTask.description,
    status: 'active',
    createdAt: new Date().toISOString().split('T')[0],
    priority: newTask.priority
  }
  tasks.value.push(task)
  newTask.title = ''
  newTask.description = ''
  newTask.priority = ''
}

function toggleStatus(task) {
  task.status = task.status === 'done' ? 'active' : 'done'
}

function deleteTask(id) {
  tasks.value = tasks.value.filter(t => t.id !== id)
}

function editTask(task) {
  const newTitle = prompt('Нова назва', task.title)
  if (newTitle) task.title = newTitle
}

function clearFilters() {
  Object.keys(filters).forEach(k => (filters[k] = ''))
}

function nextPage() {
  if (currentPage.value < totalPages.value) currentPage.value++
}
function prevPage() {
  if (currentPage.value > 1) currentPage.value--
}
</script>

<style scoped>
body {
  background: radial-gradient(circle at top left, #ffffff, #ffffff 80%);
  font-family: 'Inter', sans-serif;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 60px 0;
  color: #1f1f1f;
}

.todo-container {
  width: 800px;
  padding: 32px;
  border-radius: 24px;
  background: #eaeaea;
  box-shadow:
      20px 20px 40px #f1f1f1,
      -20px -20px 40px #ffffff;
  transition: all 0.3s ease;
}

.todo-container:hover {
  transform: translateY(-5px);
  box-shadow:
      25px 25px 50px #c2c2c2,
      -25px -25px 50px #ffffff;
}

.todo-container h1 {
  font-weight: 700;
  font-size: 32px;
  letter-spacing: -0.5px;
  margin-bottom: 28px;
  color: #333333;
  font-family: 'Inter', 'Poppins', sans-serif;}

.add-task {
  display: flex;
  gap: 12px;
  align-items: flex-start;
  margin-bottom: 20px;
}

.add-task input,
.add-task select {
  width: 200px;
  padding: 12px 16px;
  border-radius: 12px;
  border: none;
  background: #f5f5f5;
  box-shadow:
      inset 4px 4px 6px #d1d1d1,
      inset -4px -4px 6px #ffffff;
  font-size: 15px;
  color: #2e2e2e;
  transition: all 0.2s ease;
}

.add-task input:focus,
.add-task select:focus {
  outline: none;
  background: #ffffff;
  box-shadow:
      inset 2px 2px 5px #cfcfcf,
      inset -2px -2px 5px #ffffff;
}

.add-task button {
  padding: 12px 20px;
  border-radius: 12px;
  border: none;
  background: #737373;
  color: #fff;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
  flex-shrink: 0;
}

.add-task button:hover {
  background: #3c3c3c;
}

.task-list li {
  background: #ededed;
  border-radius: 16px;
  padding: 18px 20px;
  margin-bottom: 16px;
  box-shadow:
      6px 6px 12px #cfcfcf,
      -6px -6px 12px #ffffff;
  transition: all 0.25s ease;
}

.task-list li:hover {
  transform: translateY(-3px);
  box-shadow:
      8px 8px 16px #c7c7c7,
      -8px -8px 16px #ffffff;
}

.task-list li.done {
  background: #e0e0e0;
  text-decoration: line-through;
  color: #888;
}

.pagination, .counters {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 15px;
  font-weight: 500;
  font-family: 'Inter', 'Poppins', sans-serif;
}

.counters p {
  margin: 0;
}
</style>






