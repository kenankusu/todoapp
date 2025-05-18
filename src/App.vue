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
  methods: {
    updateBranches() {
      this.branches = this.$refs.branchManager.branches;
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
.popup-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
}

.popup-header h2 {
  margin: 0;
  font-size: 1.25rem;
  font-weight: bold;
  color: rgba(0, 0, 0, 0.8);
}

.popup-header .close-button {
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
</style>
