<template>
  <div id="app">
    <h1>Todo List</h1>
    <input type="text" v-model="newTodo" @keyup.enter="addTodo">
    <button @click="addTodo">Add Todo</button>
    <ul>
      <li v-for="todo in todos" :key="todo.id">
        <input type="checkbox" :checked="todo.completed" @change="toggleComplete(todo)">
        <span :class="{ completed: todo.completed }">{{ todo.task }}</span>
        <button @click="deleteTodo(todo.id)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      todos: [],
      newTodo: ''
    }
  },
  mounted() {
    this.getTodos();
  },
  methods: {
    async getTodos() {
      try {
        const response = await axios.get('http://localhost:8080/api/todos');
        this.todos = response.data;
      } catch (error) {
        console.error(error);
      }
    },
    async addTodo() {
      if (this.newTodo.trim() === '') return;

      const todo = { task: this.newTodo, completed: false };

      try {
        const response = await axios.post('http://localhost:8080/api/todos', todo);
        this.todos.push(response.data);
        this.newTodo = '';
        this.getTodos();

      } catch (error) {
        console.error(error);
      }
    },
    async toggleComplete(todo) {
      try {
        const response = await axios.put(`http://localhost:8080/api/todos/${todo.id}`, { ...todo, completed: !todo.completed });
        todo.completed = !todo.completed;
      } catch (error) {
        console.error(error);
      }
    },
    async deleteTodo(id) {
      try {
        await axios.delete(`http://localhost:8080/api/todos/${id}`);
        this.todos = this.todos.filter(todo => todo.id !== id);
      } catch (error) {
        console.error(error);
      }
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.completed {
  text-decoration: line-through;
}
</style>
