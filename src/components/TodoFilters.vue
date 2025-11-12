<template>
  <div class="filters">
    <input v-model="localText" placeholder="Пошук по тексту" />
    <select v-model="localStatus">
      <option value="all">Усі</option>
      <option value="active">Active</option>
      <option value="done">Done</option>
    </select>
    <select v-model="localPriority">
      <option value="all">Усі</option>
      <option value="low">low</option>
      <option value="medium">medium</option>
      <option value="high">high</option>
    </select>
    <button @click="clear">Очистити</button>
  </div>
</template>

<script setup>
import { toRef, watch, ref } from 'vue'
const props = defineProps({
  filterText: String,
  filterStatus: String,
  filterPriority: String
})
const emit = defineEmits(['clear'])
// using v-model:prop syntax from parent
const localText = ref(props.filterText ?? '')
const localStatus = ref(props.filterStatus ?? 'all')
const localPriority = ref(props.filterPriority ?? 'all')

// emit updates to parent via v-model:... -> update:modelValue is not used, so use $emit on change via watchers
watch(localText, (v) => {
  // update parent
  // parent bound uses v-model:filterText -> expects update:filterText event
  // but Vue's defineEmits needs explicit event names; easier: use .sync-like pattern
  // We'll emit `update:filterText` etc.
  emit('update:filterText', v)
})
watch(localStatus, (v) => emit('update:filterStatus', v))
watch(localPriority, (v) => emit('update:filterPriority', v))

function clear() {
  emit('clear')
}
</script>

<style>
.filters { display:flex; gap:8px; margin-bottom:10px; flex-wrap:wrap }
.filters input, .filters select { padding:6px; border-radius:4px; border:1px solid #ccc }
.filters button { padding:6px 8px; cursor:pointer }
</style>
