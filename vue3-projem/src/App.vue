<template>
  <div class="todo-app">
    <h1>Yapılacaklar Listesi</h1>
    <div class="input-container">
      <input v-model="newTask" @keyup.enter="addTask" placeholder="Görev ekleyin..." />
      <button @click="addTask">➕ Ekle</button>
    </div>
    <div class="filter-buttons">
      <button :class="{ active: filter === 'all' }" @click="filterTasks('all')">📋 Tümü</button>
      <button :class="{ active: filter === 'active' }" @click="filterTasks('active')">⏳ Aktif</button>
      <button :class="{ active: filter === 'completed' }" @click="filterTasks('completed')">✅ Tamamlanan</button>
    </div>
    <ul>
      <li v-for="task in filteredTasks" :key="task.id" :class="{ completed: task.completed }">
        <input class="task-checkbox" type="checkbox" v-model="task.completed" />
        <span @dblclick="editTask(task)" class="task-text">{{ task.text }}</span>
        <button class="delete-btn" @click="deleteTask(task.id)">🗑️</button>
      </li>
    </ul>
    <p v-if="tasks.length === 0" class="empty-message">🚀 Görev yok! Hemen bir tane ekleyin.</p>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue';

const newTask = ref('');
const tasks = ref([]);
const filter = ref('all');

onMounted(() => {
  tasks.value = JSON.parse(localStorage.getItem('tasks')) || [];
});

watch(tasks, (newTasks) => {
  localStorage.setItem('tasks', JSON.stringify(newTasks));
}, { deep: true });

const addTask = () => {
  if (newTask.value.trim() === '') return;
  tasks.value.push({ id: Date.now(), text: newTask.value, completed: false });
  newTask.value = '';
};

const deleteTask = (id) => {
  tasks.value = tasks.value.filter(task => task.id !== id);
};

const filterTasks = (type) => {
  filter.value = type;
};

const editTask = (task) => {
  const newText = prompt('Görevi düzenle:', task.text);
  if (newText !== null && newText.trim() !== '') {
    task.text = newText.trim();
  }
};

const filteredTasks = computed(() => {
  if (filter.value === 'active') return tasks.value.filter(task => !task.completed);
  if (filter.value === 'completed') return tasks.value.filter(task => task.completed);
  return tasks.value;
});
</script>

<style>
.todo-app {
  max-width: 400px;
  margin: auto;
  text-align: center;
  font-family: Arial, sans-serif;
  background: #f9f9f9;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}
.input-container {
  display: flex;
  gap: 10px;
  justify-content: center;
  margin-bottom: 10px;
}
input {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 5px;
  width: 70%;
}
button {
  padding: 8px 12px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  background: #007bff;
  color: white;
}
button:hover {
  background: #0056b3;
}
.filter-buttons {
  margin: 10px 0;
  display: flex;
  gap: 10px;
  justify-content: center;
}
.filter-buttons button {
  background: #ddd;
  color: black;
}
.filter-buttons .active {
  background: #007bff;
  color: white;
}
ul {
  list-style: none;
  padding: 0;
}
li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 10px;
  padding: 8px;
  background: white;
  margin: 5px 0;
  border-radius: 5px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.1);
  position: relative;
}
.task-checkbox {
  width: 20px;
  height: 20px;
  position: absolute;
  left: 10px;
}
.task-text {
  flex: 1;
  margin-left: 40px;
}
.completed .task-text {
  text-decoration: line-through;
  opacity: 0.6;
}
.delete-btn {
  background: red;
  color: white;
  padding: 5px 10px;
}
.delete-btn:hover {
  background: darkred;
}
.empty-message {
  font-style: italic;
  color: #666;
}
</style>
