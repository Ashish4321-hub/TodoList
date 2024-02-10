<template>
  <div id="app">
    <div class="wrapper">
      <h1 class="text--heading__normal welcome-message" :class="{ 'fade-in': welcomeAnimation }">Vue.js Todo List</h1>
      <div class="input-container">
        <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Add a new todo">
        <button @click="addTodo">Add</button>
      </div>
      <div class="filters">
        <button @click="filterTodos('all')">All</button>
        <button @click="filterTodos('open')">Open</button>
        <button @click="filterTodos('completed')">Completed</button>
      </div>
      <ul>
        <li class="todo-list-item" v-for="(todo, index) in filteredTodos" :key="index">
          <div class="todo-list-item--content"> 
            <input type="checkbox" v-model="todo.completed" @change="updateTodo(todo)">
            <span :class="{ 'completed': todo.completed }">{{ todo.text }}</span>
            <small>Created at: {{ todo.createdAt }}</small>
            <button class="important" @click="toggleImportant(todo)">Important</button>
            <button class="error" @click="removeTodo(todo.id)">Remove</button>
          </div>
        </li>
        <p v-if="error" class="error">{{ error }}</p>
      </ul>
      <h2 class="text--heading__green">Completed Tasks</h2>
      <ul>
        <li class="todo-list-item__completed" v-for="(todo, index) in completedTodos" :key="index">
          <span>{{ todo.text }}</span>
          <button class="important" @click="toggleImportant(todo)">Important</button>
          <button class="error" @click="removeTodo(todo.id)">Remove</button>
        </li>
      </ul>
    </div>
    <div v-if="showFireworks" class="firecracker"></div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      newTodo: '',
      todos: [],
      error: '',
      filter: 'all',
      welcomeAnimation: false,
      showFireworks: false
    };
  },
  computed: {
    completedTodos() {
      return this.todos.filter(todo => todo.completed);
    },
    openTodos() {
      return this.todos.filter(todo => !todo.completed);
    },
    filteredTodos() {
      if (this.filter === 'open') {
        return this.openTodos;
      } else if (this.filter === 'completed') {
        return this.completedTodos;
      } else {
        return this.todos;
      }
    }
  },
  mounted() {
    this.loadTodos();
    this.animateWelcome();
  },
  methods: {
    async addTodo() {
      if (this.newTodo.trim() === '') {
        this.error = 'Task cannot be empty!';
        setTimeout(() => {
          this.error = '';
        }, 2500);
        return;
      }
      const todo = {
        text: this.newTodo,
        completed: false,
        important: false,
        createdAt: new Date().toLocaleString()
      };
      try {
        const response = await axios.post('http://localhost:3001/todos', todo);
        this.todos.push(response.data);
        this.error = '';
        this.saveTodos();
      } catch (error) {
        console.error('Error adding todo:', error);
      }
      this.newTodo = '';
    },
    async updateTodo(todo) {
      try {
        await axios.put(`http://localhost:3001/todos/${todo.id}`, todo);
        this.saveTodos();
      } catch (error) {
        console.error('Error updating todo:', error);
      }
    },
    async removeTodo(id) {
      try {
        await axios.delete(`http://localhost:3001/todos/${id}`);
        this.todos = this.todos.filter(todo => todo.id !== id);
        this.saveTodos();
      } catch (error) {
        console.error('Error deleting todo:', error);
      }
    },
    async toggleImportant(todo) {
      todo.important = !todo.important;
      this.updateTodo(todo);
    },
    async filterTodos(filter) {
      this.filter = filter;
    },
    async loadTodos() {
      try {
        const savedTodos = localStorage.getItem('todos');
        if (savedTodos) {
          this.todos = JSON.parse(savedTodos);
        }
      } catch (error) {
        console.error('Error loading todos:', error);
      }
    },
    saveTodos() {
      localStorage.setItem('todos', JSON.stringify(this.todos));
    },
    animateWelcome() {
      setTimeout(() => {
        this.welcomeAnimation = true;
      }, 500);
    },
    startFireworks() {
      this.showFireworks = true;
    }
  }
};
</script>

<style scoped>
/* Your existing styles can remain unchanged */

/* Welcome message animation */
.welcome-message.fade-in {
  opacity: 1;
  transition: opacity 1s ease;
}

/* Firecracker animation */
.firecracker {
  width: 10px;
  height: 50px;
  background-color: #ff0000; /* Red color */
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  animation: firecrackerAnimation 0.5s ease-in-out forwards;
}

@keyframes firecrackerAnimation {
  0% { height: 0; }
  50% { height: 100px; }
  100% { height: 50px; }
}
#app {
  display: flex;
  align-items: center;
  flex-direction: column;
  justify-content: center;
  padding: 20px;
  color: #333;
  background-color: #f0f0f0; /* Light gray background */
  background-image: url('data:image/svg+xml,%3Csvg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100"%3E%3Cg fill="%23d9d9d9" fill-opacity="0.4"%3E%3Crect x="0" y="0" width="20" height="20"/%3E%3Crect x="25" y="0" width="20" height="20"/%3E%3Crect x="50" y="0" width="20" height="20"/%3E%3Crect x="75" y="0" width="20" height="20"/%3E%3Crect x="0" y="25" width="20" height="20"/%3E%3Crect x="25" y="25" width="20" height="20"/%3E%3Crect x="50" y="25" width="20" height="20"/%3E%3Crect x="75" y="25" width="20" height="20"/%3E%3Crect x="0" y="50" width="20" height="20"/%3E%3Crect x="25" y="50" width="20" height="20"/%3E%3Crect x="50" y="50" width="20" height="20"/%3E%3Crect x="75" y="50" width="20" height="20"/%3E%3Crect x="0" y="75" width="20" height="20"/%3E%3Crect x="25" y="75" width="20" height="20"/%3E%3Crect x="50" y="75" width="20" height="20"/%3E%3Crect x="75" y="75" width="20" height="20"/%3E%3C/g%3E%3C/svg%3E');
}

.text--heading__normal {
  font-size: 28px;
  font-weight: bold;
  color: #333;
}

.text--heading__green {
  font-size: 24px;
  font-weight: bold;
  color: #4caf50;
}

.wrapper {
  max-width: 600px;
  padding: 20px;
  background-color: #fff; /* White background for the content */
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.input-container {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.input-container input {
  flex: 1;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.input-container button {
  padding: 10px 20px;
  font-size: 16px;
  background-color: #4caf50;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.input-container button:hover {
  background-color: #45a049;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin-bottom: 15px;
}

.todo-list-item,
.todo-list-item__completed {
  display: flex;
  align-items: center;
  padding: 10px;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.3s ease;
}

.todo-list-item:hover,
.todo-list-item__completed:hover {
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
}

.todo-list-item--content {
  flex: 1;
  display: flex;
  align-items: center;
}

.todo-list-item--content input {
  margin-right: 10px;
}

.error {
  color: red;
  margin-top: 5px;
}
.input-container button,
.filters button,
.todo-list-item--content button {
  padding: 10px 20px;
  font-size: 16px;
  background-color: #4caf50;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin-right: 5px;
}

.input-container button:hover,
.filters button:hover,
.todo-list-item--content button:hover {
  background-color: #45a049;
}

.important {
  background-color: #ff9800;
}

.important:hover {
  background-color: #f57c00;
}

.error {
  color: red;
  margin-top: 5px;
}
/* Animation for todo items */
.todo-list-item {
  animation: slideInUp 0.5s ease forwards;
}

@keyframes slideInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Fade animation for removing todo items */
.todo-list-item.leave-active {
  transition: opacity 0.5s;
}

.todo-list-item.leave-to {
  opacity: 0;
}

/* Animation for todo list container */
.wrapper {
  animation: fadeIn 0.5s ease forwards;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

</style>


 