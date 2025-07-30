<template>
  <div class="min-h-screen bg-gray-100 flex flex-col items-center p-6">
    <div class="w-full max-w-lg bg-white rounded-lg shadow-md p-6">
      <h1 class="text-2xl font-bold mb-4 text-center">Todo List</h1>

      <!-- Form tambah todo -->
      <form @submit.prevent="addTodo" class="flex gap-2 mb-6">
        <input v-model="newTodo" type="text" placeholder="Enter todo"
          class="flex-1 p-2 border border-gray-300 rounded focus:outline-none focus:ring-2 focus:ring-blue-400" required />
        <button type="submit"
          class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 transition">Add</button>
      </form>

      <!-- List todos -->
      <ul class="space-y-3">
        <li v-for="todo in todos" :key="todo.id"
          class="flex justify-between items-center p-3 bg-gray-50 rounded hover:bg-gray-100">
          <label class="flex items-center gap-2">
            <input type="checkbox" v-model="todo.completed" @change="toggleComplete(todo)"
              class="h-4 w-4 text-blue-500 rounded" />
            <span :class="{ 'line-through text-gray-500': todo.completed }">{{ todo.title }}</span>
          </label>
          <button @click="deleteTodo(todo.id)" class="text-red-500 cursor-pointer hover:text-red-700">x</button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const api = axios.create({
  baseURL: "http://localhost:8080",
});

const todos = ref([]);
const newTodo = ref("");

const fetchTodos = async () => {
  const res = await api.get("/todos");
  todos.value = res.data;
};

onMounted(fetchTodos);

const addTodo = async () => {
  await api.post("/todos", { title: newTodo.value, completed: false });
  newTodo.value = "";
  await fetchTodos();
};

const toggleComplete = async (todo) => {
  await api.put(`/todos/${todo.id}`, {
    title: todo.title,
    completed: todo.completed,
  });
};

const deleteTodo = async (id) => {
  await api.delete(`/todos/${id}`);
  await fetchTodos();
};
</script>
