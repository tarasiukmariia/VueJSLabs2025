<template>
  <li class="todo-item">
    <div class="left">
      <input type="checkbox" :checked="todo.status === 'done'" @change="emitToggle" />
      <div class="content">
        <div v-if="!editing">
          <div :class="{ done: todo.status === 'done' }" class="title">{{ todo.title }}</div>
          <div class="meta">{{ todo.description }} • {{ todo.priority }} • {{ formattedDate }}</div>
        </div>

        <div v-else class="edit">
          <input v-model="local.title" />
          <input v-model="local.description" />
          <select v-model="local.priority">
            <option value="low">low</option>
            <option value="medium">medium</option>
            <option value="high">high</option>
          </select>
        </div>
      </div>
    </div>

    <div class="actions">
      <button v-if="!editing" @click="editing = true">Edit</button>
      <button v-else @click="save">Save</button>
      <button @click="remove" class="del">Delete</button>
    </div>
  </li>
</template>

<script setup>
import { ref, computed, reactive, toRaw } from 'vue'
const props = defineProps({
  todo: { type: Object, required: true }
})
const emit = defineEmits(['toggle', 'delete', 'save'])
const editing = ref(false)
const local = reactive({ title: props.todo.title, description: props.todo.description, priority: props.todo.priority })

const formattedDate = computed(() => new Date(props.todo.createdAt).toLocaleString())

function emitToggle() {
  emit('toggle', props.todo.id)
}

function remove() {
  emit('delete', props.todo.id)
}

function save() {
  const edited = {
    ...toRaw(props.todo),
    title: local.title.trim(),
    description: local.description.trim(),
    priority: local.priority
  }
  emit('save', edited)
  editing.value = false
}
</script>

<style>
.todo-item { display:flex; justify-content:space-between; align-items:center; padding:8px 6px; border-bottom:1px solid #eee }
.left { display:flex; gap:8px; align-items:flex-start }
.content { min-width: 300px }
.title { font-weight:700 }
.title.done { text-decoration: line-through; color:#777 }
.meta { font-size:12px; color:#666 }
.actions { display:flex; gap:6px }
.actions button { padding:6px 8px; border-radius:4px; cursor:pointer }
.actions .del { color:#c33 }
.edit input { margin-bottom:6px; display:block; padding:6px; border-radius:4px; border:1px solid #ccc }
</style>
