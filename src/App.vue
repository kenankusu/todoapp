<template>
  <div class="container">
    <div class="div-left">
      <h1 class="date-heading">{{ currentMonthYear }}</h1>
      <MonthCalendar />
      <div class="goals-header">
        <h1 class="goals-heading">Ziele</h1>
      </div>
      <GoalsManager :branches="branches" />
    </div>
    <div class="div-right">
      <div class="todo-header">
        <h1 class="date-heading">To-Do</h1>
      </div>
      <BranchManager ref="branchManager" @branches-updated="updateBranches" />
    </div>
  </div>
</template>

<script>
import MonthCalendar from './components/Calendar.vue'
import BranchManager from './components/BranchManager.vue'
import GoalsManager from './components/GoalsManager.vue'

export default {
  name: 'App',
  components: {
    MonthCalendar,
    BranchManager,
    GoalsManager
  },
  data() {
    return {
      branches: []
    }
  },
  created() {
    // Initialize branches from localStorage
    this.updateBranches();
  },
  methods: {
    updateBranches() {
      const storedBranches = JSON.parse(localStorage.getItem('branches') || '[]');
      this.branches = [...storedBranches]; // Create a new array to ensure reactivity
    }
  },
  watch: {
    // Watch localStorage for changes
    '$refs.branchManager.branches': {
      deep: true,
      handler() {
        this.updateBranches();
      }
    }
  },
  computed: {
    currentMonthYear() {
      const date = new Date();
      const month = date.toLocaleString('default', { month: 'long' });
      const year = date.getFullYear();
      return `${month} ${year}`;
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  margin: 0;
  overflow: hidden;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  height: 100vh;
  overflow: hidden;
}

.container {
  display: flex;
  height: 100vh;
  overflow: hidden;
}

.div-left {
  width: 50%;
  background-color: white;
  display: flex;
  flex-direction: column;
  height: 100vh;
  overflow-y: auto;
  overflow-x: hidden;

  /* Webkit (Chrome, Safari, Edge) scrollbar styling */
  &::-webkit-scrollbar {
    width: 8px;
  }

  &::-webkit-scrollbar-track {
    background: transparent;
  }

  &::-webkit-scrollbar-thumb {
    background-color: rgba(0, 0, 0, 0.2);
    border-radius: 4px;
  }

  &::-webkit-scrollbar-thumb:hover {
    background-color: rgba(0, 0, 0, 0.3);
  }

  /* Firefox scrollbar styling */
  scrollbar-width: thin;
  scrollbar-color: rgba(0, 0, 0, 0.2) transparent;
}

/* When hovering over the scrollable area */
.div-left:hover {
  &::-webkit-scrollbar-thumb {
    background-color: rgba(0, 0, 0, 0.3);
  }
  scrollbar-color: rgba(0, 0, 0, 0.3) transparent;
}

.div-right {
  width: 50%;
  background-color: #ffffff;
  height: 100vh;
  overflow-y: auto;
  overflow-x: hidden;

  /* Webkit (Chrome, Safari, Edge) scrollbar styling */
  &::-webkit-scrollbar {
    width: 8px;
  }

  &::-webkit-scrollbar-track {
    background: transparent;
  }

  &::-webkit-scrollbar-thumb {
    background-color: rgba(0, 0, 0, 0.2);
    border-radius: 4px;
  }

  &::-webkit-scrollbar-thumb:hover {
    background-color: rgba(0, 0, 0, 0.3);
  }

  /* Firefox scrollbar styling */
  scrollbar-width: thin;
  scrollbar-color: rgba(0, 0, 0, 0.2) transparent;
}

/* When hovering over the scrollable area */
.div-right:hover {
  &::-webkit-scrollbar-thumb {
    background-color: rgba(0, 0, 0, 0.3);
  }
  scrollbar-color: rgba(0, 0, 0, 0.3) transparent;
}

.date-heading {
  text-align: left;
  font-size: 2.5rem;
  margin: 2rem 2rem;
  font-weight: bold;
}

.goals-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 2rem;
}

.goals-heading {
  text-align: left;
  font-size: 2.5rem;
  font-weight: bold;
  margin: 2rem 0;
}

.todo-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 2rem;
}

/* Common popup styling */
.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.popup-content {
  background-color: white;
  padding: 2rem;
  border-radius: 8px;
  width: 90%;
  max-width: 400px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.popup-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.popup-title {
  margin: 0;
  font-size: 1.25rem;
  font-weight: bold;
  color: rgba(0, 0, 0, 0.8);
}

.close-popup-button {
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  padding: 5px 10px;
  color: #666;
}

/* Common add button styling */
.add-button {
  width: 2.5rem;
  height: 2.5rem;
  border-radius: 50%;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  top: -5rem;
  right: 2rem;
  background-color: white;
  color: rgb(101, 101, 101);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s, box-shadow 0.2s;
}

.add-button:hover {
  transform: scale(1.05);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
}

/* Common box styling */
.content-box {
  padding: 1.5rem;
  border-radius: 8px;
  width: 100%;
  margin-bottom: 1rem;
  position: relative;
  display: flex;
  flex-direction: column;
  transition: transform 0.2s;
}

.content-box:hover {
  transform: scale(1.01);
}

.content-box-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.content-box-header h3 {
  margin: 0;
  color: rgba(0, 0, 0, 0.8);
  font-size: 1.1rem;
  font-weight: 500;
}

.content-box-delete {
  opacity: 0;
  background: none;
  border: none;
  color: rgba(0, 0, 0, 0.6);
  font-size: 1.5rem;
  cursor: pointer;
  padding: 0.2rem 0.5rem;
  border-radius: 4px;
  transition: opacity 0.2s, background-color 0.2s;
}

.content-box:hover .content-box-delete {
  opacity: 1;
}

.content-box-delete:hover {
  background-color: rgba(0, 0, 0, 0.1);
}

.content-items {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.content-item {
  background-color: rgba(255, 255, 255, 0.9);
  padding: 0.5rem;
  border-radius: 4px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.content-item-text {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  flex-grow: 1;
}

.content-item.completed .content-item-text span {
  text-decoration: line-through;
  color: #666;
}

.content-item-delete {
  background: none;
  border: none;
  color: #666;
  cursor: pointer;
  font-size: 1.2rem;
  padding: 0 0.3rem;
}

.content-add {
  margin-top: auto;
  display: flex;
  gap: 0.5rem;
}

.content-add-input {
  flex-grow: 1;
  padding: 0.5rem;
  border: none;
  border-radius: 4px;
  background-color: rgba(255, 255, 255, 0.9);
}

.content-add-button {
  background-color: rgba(255, 255, 255, 0.9);
  border: none;
  border-radius: 4px;
  width: 2rem;
  cursor: pointer;
  font-size: 1.2rem;
  color: #666;
}

.content-add-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
