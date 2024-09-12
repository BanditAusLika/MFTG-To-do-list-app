<template>
  <div class="todo-app">
    <h1 class="app-title">MFTG To-Do List</h1>

    <!-- Task input section -->
    <div class="task-input">
      <input v-model="newTask" @keyup.enter="addTask" placeholder="Enter a task..." />
      <select v-model="newTaskPriority">
        <option value="low">Low</option>
        <option value="medium">Medium</option>
        <option value="high">High</option>
      </select>
      <button @click="addTask">Add Task</button>
    </div>

    <!-- Task filters -->
    <div class="filters">
      <button @click="taskFilter = 'all'">All</button>
      <button @click="taskFilter = 'active'">Active</button>
      <button @click="taskFilter = 'completed'">Completed</button>
    </div>

    <!-- Task list with priorities and filters -->
    <ul class="task-list">
      <li v-for="(task, index) in filteredTasks" :key="index" :class="{ completed: task.completed }" :style="taskStyle(task.priority)">
        <span @click="editTask(index)" v-if="!task.editing">{{ task.text }}</span>
        <input v-if="task.editing" v-model="task.text" @keyup.enter="finishEditTask(index)" />
        <button @click="completeTask(index)" v-if="!task.completed">Mark Complete</button>
        <button @click="deleteTask(index)">Delete</button>
      </li>
    </ul>

    <footer class="footer">
      <p>Date: {{ currentDate }} | Time: {{ currentTime }}</p>
      <p>Created By MFTG</p>
    </footer>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTask: '', // Stores the new task being entered
      newTaskPriority: 'low', // Stores the priority of the new task
      tasks: [], // Stores all the tasks
      taskFilter: 'all', // Stores which filter is active (all, active, completed)
      currentDate: '', // Stores the current date
      currentTime: '', // Stores the current time
    };
  },
  computed: {
    filteredTasks() {
      if (this.taskFilter === 'all') {
        return this.tasks;
      } else if (this.taskFilter === 'active') {
        return this.tasks.filter(task => !task.completed);
      } else {
        return this.tasks.filter(task => task.completed);
      }
    },
  },
  mounted() {
    this.loadTasks();
    this.updateTime();
    setInterval(this.updateTime, 1000);
  },
  methods: {
    addTask() {
      if (this.newTask.trim() !== '') {
        this.tasks.push({ text: this.newTask, priority: this.newTaskPriority, completed: false, editing: false });
        this.newTask = '';
        this.newTaskPriority = 'low';
        this.saveTasks();
      }
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks();
    },
    completeTask(index) {
      this.tasks[index].completed = true;
      this.saveTasks();
    },
    editTask(index) {
      this.tasks[index].editing = true;
    },
    finishEditTask(index) {
      this.tasks[index].editing = false;
      this.saveTasks();
    },
    saveTasks() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },
    loadTasks() {
      const savedTasks = localStorage.getItem('tasks');
      if (savedTasks) {
        this.tasks = JSON.parse(savedTasks);
      }
    },
    updateTime() {
      const now = new Date();
      this.currentDate = now.toLocaleDateString();
      this.currentTime = now.toLocaleTimeString();
    },
    taskStyle(priority) {
      if (priority === 'high') {
        return { color: 'red', fontWeight: 'bold' };
      } else if (priority === 'medium') {
        return { color: 'orange' };
      } else {
        return { color: 'green' };
      }
    },
  },
};
</script>

<style scoped>
/* Import Orbitron font */
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap');

/* App container with improved styling */
.todo-app {
  background-color: #101010;
  color: #00ffff;
  font-family: 'Orbitron', sans-serif;
  padding: 30px;
  text-align: center;
  width: 60%;
  margin: 20px auto;
  box-shadow: 0 0 25px rgba(0, 255, 255, 0.3);
  border-radius: 12px;
  min-height: 80vh;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  border: 2px solid #00ffff;
}

/* Title with a more polished glow effect */
.app-title {
  font-size: 3em;
  margin-bottom: 20px;
  text-shadow: 0 0 15px #00ffff, 0 0 30px #00ffff;
}

/* Input and buttons styling for cleaner look */
.task-input {
  margin-bottom: 30px;
}

.task-input input {
  padding: 12px;
  width: 65%;
  border: none;
  background-color: #191919;
  color: #00ffff;
  font-family: 'Orbitron', sans-serif;
  outline: none;
  box-shadow: 0 0 12px #00ffff;
  transition: box-shadow 0.3s ease, background-color 0.3s ease;
}

.task-input input:focus {
  box-shadow: 0 0 20px #00ffff, 0 0 40px #ff00ff;
  background-color: #202020;
}

/* Select priority styling */
.task-input select {
  margin-left: 12px;
  padding: 10px;
  background-color: #202020;
  color: #00ffff;
  border: none;
  outline: none;
  box-shadow: 0 0 12px #00ffff;
  font-family: 'Orbitron', sans-serif;
}

/* Add task button */
.task-input button {
  padding: 12px 18px;
  margin-left: 12px;
  background-color: #101010;
  color: #00ffff;
  border: none;
  cursor: pointer;
  transition: 0.3s ease;
  box-shadow: 0 0 12px #00ffff;
}

.task-input button:hover {
  background-color: #ff00ff;
  box-shadow: 0 0 20px #ff00ff, 0 0 30px #00ffff;
}

/* Task list styling */
.task-list {
  list-style: none;
  padding: 0;
}

.task-list li {
  margin: 12px 0;
  padding: 12px;
  background-color: #191919;
  border: 1px solid #00ffff;
  transition: 0.3s ease;
  box-shadow: 0 0 12px #00ffff;
}

.task-list li.completed {
  text-decoration: line-through;
  color: #008000;
}

.task-list li span {
  cursor: pointer;
  transition: box-shadow 0.3s ease;
}

.task-list li span:hover {
  box-shadow: 0 0 20px #00ffff, 0 0 30px #ff00ff;
}

.task-list li input {
  width: 75%;
  background-color: #222222;
  color: #00ffff;
  border: none;
  outline: none;
}

/* Task list buttons */
.task-list li button {
  margin-left: 12px;
  padding: 6px 12px;
  background-color: #101010;
  color: #ff00ff;
  border: none;
  cursor: pointer;
  box-shadow: 0 0 12px #ff00ff;
}

.task-list li button:hover {
  background-color: #ff00ff;
  color: #101010;
  box-shadow: 0 0 20px #ff00ff, 0 0 30px #00ffff;
}

/* Filters button section styling */
.filters {
  margin: 20px 0;
}

.filters button {
  padding: 10px 20px;
  background-color: #101010;
  color: #00ffff;
  border: 1px solid #00ffff;
  margin: 0 5px;
  cursor: pointer;
  font-family: 'Orbitron', sans-serif;
  box-shadow: 0 0 10px #00ffff;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.filters button:hover {
  background-color: #ff00ff;
  box-shadow: 0 0 20px #ff00ff, 0 0 30px #00ffff;
}

/* Footer styling */
.footer {
  margin-top: 40px;
}

.footer p {
  font-size: 1.3em;
  text-shadow: 0 0 15px #00ffff;
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .todo-app {
    width: 85%;
  }

  .task-input input {
    width: 70%;
  }

  .app-title {
    font-size: 2.5em;
  }
}

@media (max-width: 480px) {
  .todo-app {
    width: 90%;
  }

  .task-input input {
    width: 75%;
  }

  .app-title {
    font-size: 2em;
  }
}
</style>
