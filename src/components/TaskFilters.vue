<template>
  <div class="filters">
    <h3>Пошук</h3>
    <div class="filters-row">
      <input v-model="filters.title" placeholder="Назва..." />
      <input v-model="filters.description" placeholder="Опис..." />
      <select v-model="filters.status">
        <option value="">Статус</option>
        <option value="active">Активні</option>
        <option value="done">Виконані</option>
      </select>
      <select v-model="filters.priority">
        <option value="">Пріоритет</option>
        <option value="low">Низький</option>
        <option value="medium">Середній</option>
        <option value="high">Високий</option>
      </select>
    </div>

    <div class="filters-row">
      <label>Від:</label>
      <input type="date" v-model="filters.fromDate" />
      <label>До:</label>
      <input type="date" v-model="filters.toDate" />
      <button class="clear-btn" @click="$emit('clear')">Очистити фільтри</button>
    </div>
  </div>
</template>

<script setup>
const filters = defineModel('filters', { type: Object })
</script>

<style scoped>
.filters {
  margin: 20px 0;
  padding: 20px;
  border-radius: 20px;
  background: #eaeaea;
  box-shadow:
      8px 8px 16px #d1d1d1,
      -8px -8px 16px #ffffff;
  font-family: 'Inter', 'Poppins', sans-serif;
}

.filters h3 {
  margin-bottom: 15px;
  color: #333333;
  font-size: 20px;
  font-weight: 600;
}

.filters-row {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  margin-bottom: 15px;
  align-items: flex-start;
}

.filters-row input,
.filters-row select {
  padding: 10px 14px;
  border-radius: 12px;
  border: none;
  background: #f5f5f5;
  box-shadow:
      inset 4px 4px 6px #d1d1d1,
      inset -4px -4px 6px #ffffff;
  font-size: 14px;
  color: #2e2e2e;
  transition: all 0.2s ease;
  flex-shrink: 0;
}

.filters-row input:focus,
.filters-row select:focus {
  outline: none;
  background: #ffffff;
  box-shadow:
      inset 2px 2px 5px #cfcfcf,
      inset -2px -2px 5px #ffffff;
}

.clear-btn {
  padding: 10px 18px;
  border-radius: 12px;
  border: none;
  background: #5a5a5a;
  color: #fff;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
  flex-shrink: 0;
}

.clear-btn:hover {
  background: #353535;
}

.filters-row label {
  margin-top: 12px;
  font-weight: 500;
  color: #2e2e2e;
}
</style>
