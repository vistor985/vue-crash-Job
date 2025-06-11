<script setup>
import { ref, onMounted } from 'vue';

const message = ref('Hello Vue!');
const status = ref('pending');
const tasks = ref([
  { id: 1, title: 'Task 1', completed: false },
  { id: 2, title: 'Task 2', completed: true },
  { id: 3, title: 'Task 3', completed: false },
]);
const newTask = ref('');

const toggleStatus = () => {
  if (status.value === 'active') {
    status.value = 'pending';
  } else if (status.value === 'pending') {
    status.value = 'inactive';
  } else {
    status.value = 'active';
  }
};

const addTask = () => {
  if (newTask.value.trim() !== '') {
    tasks.value.push({
      id: tasks.value.length + 1,
      title: newTask.value,
      completed: false,
    });
    newTask.value = '';
  }
};

const deleteTask = (index) => {
  tasks.value.splice(index, 1);
};

onMounted(async () => {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/todos');
    const data = await response.json();
    tasks.value = data.slice(0, 5).map((task) => ({
      id: task.id,
      title: task.title,
      completed: task.completed,
    }));
    console.log('Tasks loaded successfully:', tasks.value);
  } catch (error) {
    console.error('Error during component mount:', error);
  }
});
</script>
<template>
  <h1>{{ message }}</h1>
  <p v-if="status === 'active'">User is active</p>
  <p v-else-if="status === 'pending'">User is pending</p>
  <p v-else>User is inactive</p>

  <form @submit.prevent="addTask">
    <label for="newTask">Add Task</label>
    <input type="text" id="newTask" v-model="newTask" />
    <button type="submit">Submit</button>
  </form>

  <ul>
    <li v-for="(task, index) in tasks" :key="task.id">
      {{ task.title }} - <span v-if="task.completed">Completed</span
      ><span v-else>Not Completed</span>
      <button @click="deleteTask(index)">x</button>
    </li>
  </ul>
  <button @click="toggleStatus">Change Status</button>
</template>
