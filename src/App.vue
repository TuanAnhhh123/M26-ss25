<script setup>
import { ref, computed, watch } from 'vue'

const newTask = ref('')
const filter = ref('all')

const tasks = ref(JSON.parse(localStorage.getItem('tasks')) || [
  { id: 1, title: 'Quét nhà', completed: false, isEditing: false },
  { id: 2, title: 'Giặt quần áo', completed: true, isEditing: false },
  { id: 3, title: 'Nấu cơm', completed: false, isEditing: false },
])

watch(tasks, (newTasks) => {
  localStorage.setItem('tasks', JSON.stringify(newTasks))
}, { deep: true })

const addTask = () => {
  if (newTask.value.trim()) {
    tasks.value.push({
      id: Date.now(),
      title: newTask.value,
      completed: false,
      isEditing: false,
    })
    newTask.value = ''
  }
}

const filteredTasks = computed(() => {
  if (filter.value === 'all') return tasks.value
  if (filter.value === 'completed') return tasks.value.filter(task => task.completed)
  if (filter.value === 'inProgress') return tasks.value.filter(task => !task.completed)
})

const deleteTask = (id) => {
  tasks.value = tasks.value.filter(task => task.id !== id)
}

const deleteCompletedTasks = () => {
  tasks.value = tasks.value.filter(task => !task.completed)
}

const deleteAllTasks = () => {
  tasks.value = []
}

const toggleEditTask = (task) => {
  task.isEditing = !task.isEditing
}

const updateTaskTitle = (task, newTitle) => {
  task.title = newTitle
  task.isEditing = false
}
</script>

<template>
  <div class="task-manager">
    <h2>Quản lý công việc</h2>
    
    
    <div class="task-input">
      <input v-model="newTask" type="text" placeholder="Nhập tên công việc" />
      <button @click="addTask">Thêm công việc</button>
    </div>

    
    <div class="filter-buttons">
      <button @click="filter = 'all'" :class="{ active: filter === 'all' }">Tất cả</button>
      <button @click="filter = 'completed'" :class="{ active: filter === 'completed' }">Hoàn thành</button>
      <button @click="filter = 'inProgress'" :class="{ active: filter === 'inProgress' }">Đang thực hiện</button>
    </div>

   
    <ul class="task-list">
      <li v-for="task in filteredTasks" :key="task.id">
        <input type="checkbox" v-model="task.completed" />

        <span v-if="!task.isEditing" :class="{ completed: task.completed }">{{ task.title }}</span>
        <input v-if="task.isEditing" v-model="task.title" @blur="updateTaskTitle(task, task.title)" @keyup.enter="updateTaskTitle(task, task.title)" />

       
        <button @click="toggleEditTask(task)">
          <span v-if="!task.isEditing">🖉</span> 
          <span v-if="task.isEditing">💾</span> 
        </button>
        <button @click="deleteTask(task.id)">🗑</button>
      </li>
    </ul>

    
    <div class="bulk-actions">
      <button @click="deleteCompletedTasks">Xóa công việc hoàn thành</button>
      <button @click="deleteAllTasks">Xóa tất cả công việc</button>
    </div>
  </div>
</template>

<style scoped>

.task-manager {
  max-width: 400px;
  margin: 50px auto;
  padding: 20px;
  border-radius: 10px;
  background-color: #f7f8fc;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  text-align: center;
  font-family: 'Arial', sans-serif;
}

h2 {
  font-size: 24px;
  color: #333;
  margin-bottom: 20px;
}

.task-input {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.task-input input {
  padding: 12px;
  width: 70%;
  border: 2px solid #ddd;
  border-radius: 5px;
  font-size: 16px;
  outline: none;
  transition: border-color 0.3s ease;
}

.task-input input:focus {
  border-color: #007bff;
}

.task-input button {
  padding: 12px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-left: 10px;
  font-size: 16px;
  transition: background-color 0.3s ease;
}

.task-input button:hover {
  background-color: #0056b3;
}

.filter-buttons {
  margin-bottom: 20px;
}

.filter-buttons button {
  padding: 10px 20px;
  border: none;
  border-radius: 20px;
  background-color: #e0e0e0;
  color: #333;
  font-size: 14px;
  cursor: pointer;
  margin: 0 5px;
  transition: background-color 0.3s ease;
}

.filter-buttons button.active {
  background-color: #007bff;
  color: white;
}

.filter-buttons button:hover {
  background-color: #007bff;
  color: white;
}

.task-list {
  list-style: none;
  padding: 0;
  margin-bottom: 20px;
}

.task-list li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 15px;
  background-color: white;
  border: 1px solid #ddd;
  border-radius: 8px;
  margin-bottom: 10px;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.task-list li:hover {
  background-color: #f1f1f1;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

.task-list input[type="checkbox"] {
  margin-right: 10px;
}

.task-list .completed {
  text-decoration: line-through;
  color: #888;
}

.task-list button {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 20px; 
  margin-left: 10px;
  color: #555;
  transition: color 0.3s ease;
}

.task-list button:hover {
  color: #007bff;
}

.bulk-actions {
  margin-top: 20px;
}

.bulk-actions button {
  padding: 12px 20px;
  background-color: #ff5c5c;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin: 5px;
  font-size: 16px;
  transition: background-color 0.3s ease;
}

.bulk-actions button:hover {
  background-color: #e64545;
}

</style>
