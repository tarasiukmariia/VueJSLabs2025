<template>
  <li :class="{ done: task.status === 'done' }" class="task-item">
    <div class="task-header">
      <strong>{{ task.title }}</strong>
      <small>({{ task.priority }})</small>
    </div>
    <p>{{ task.description }}</p>
    <small>Дата: {{ task.createdAt }}</small>

    <div class="task-buttons">
      <button @click="$emit('toggle', task)">
        {{ task.status === 'done' ? 'Позначити активною' : 'Позначити виконаною' }}
      </button>
      <button @click="$emit('edit', task)">Редагувати</button>
      <button @click="$emit('delete', task.id)">Видалити</button>
    </div>
  </li>
</template>

<script setup>
defineProps({ task: Object })
</script>

<style scoped>
.task-item {
  list-style: none;
  margin-bottom: 16px;
  padding: 20px;
  border-radius: 16px;
  background: #eaeaea;
  box-shadow:
      6px 6px 12px #d1d1d1,
      -6px -6px 12px #ffffff;
  transition: all 0.25s ease;
  font-family: 'Inter', 'Poppins', sans-serif;
}

.task-item:hover {
  transform: translateY(-2px);
  box-shadow:
      8px 8px 16px #cfcfcf,
      -8px -8px 16px #ffffff;
}

.task-item.done {
  text-decoration: line-through;
  color: #888;
  background: #f0f0f0;
}

.task-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
}

.task-header strong {
  font-size: 16px;
}

.task-header small {
  font-size: 14px;
  color: #555;
}

.task-item p {
  margin: 6px 0;
  font-size: 14px;
  color: #2e2e2e;
}

.task-item small {
  font-size: 12px;
  color: #555;
}

.task-buttons {
  margin-top: 12px;
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.task-buttons button {
  padding: 8px 16px;
  border-radius: 12px;
  border: none;
  background: #575757;
  color: #fff;
  font-weight: 600;
  font-family: 'Inter', 'Poppins', sans-serif;
  cursor: pointer;
  transition: all 0.2s ease;
}

.task-buttons button:hover {
  background: #2c2c2c;
}
</style>
