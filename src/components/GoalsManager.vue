<template>
  <div class="goals-manager">
    <!-- Add Goal Button -->
    <button class="add-button" @click="showGoalForm = true">+</button>

    <!-- Branch Selection Popup -->
    <div v-if="showGoalForm" class="popup-overlay">
      <div class="popup-content">
        <div class="popup-header">
          <h2 class="popup-title">Neue Ziele</h2>
          <button class="close-popup-button" @click="showGoalForm = false">&times;</button>
        </div>
        <div class="form-content">
          <div class="form-group">
            <select v-model="selectedBranchId" class="branch-select" @change="handleBranchSelect">
              <option value="" disabled>Branch auswählen</option>
              <option 
                v-for="branch in branches" 
                :key="branch.id" 
                :value="branch.id"
                :style="{ backgroundColor: branch.color }"
              >
                {{ branch.name }}
              </option>
            </select>
          </div>
        </div>
      </div>
    </div>

    <!-- Goals Display -->
    <div class="goals-container">
      <div 
        v-for="goal in goals" 
        :key="goal.id"
        class="content-box"
        :style="{ backgroundColor: getBranchColor(goal.branchId) }"
      >
        <div class="content-box-header">
          <h3>{{ getBranchName(goal.branchId) }}</h3>
          <button class="content-box-delete" @click="deleteGoal(goal.id)">&times;</button>
        </div>
        <div class="content-items">
          <!-- Active Todos -->
          <div v-for="todo in activeTodos(goal)" :key="todo.id" class="content-item">
            <div class="content-item-text">
              <input 
                type="checkbox" 
                :checked="todo.completed"
                @change="toggleTodo(goal.id, todo.id)"
                class="todo-checkbox"
              />
              <span>{{ todo.text }}</span>
            </div>
            <button class="content-item-delete" @click="deleteTodo(goal.id, todo.id)">&times;</button>
          </div>

          <!-- Completed Todos -->
          <div v-if="completedTodos(goal).length > 0" class="completed-todos-section">
            <div v-for="todo in completedTodos(goal)" :key="todo.id" class="content-item completed">
              <div class="content-item-text">
                <input 
                  type="checkbox" 
                  :checked="todo.completed"
                  @change="toggleTodo(goal.id, todo.id)"
                  class="todo-checkbox"
                />
                <span>{{ todo.text }}</span>
              </div>
              <button class="content-item-delete" @click="deleteTodo(goal.id, todo.id)">&times;</button>
            </div>
          </div>
        </div>
        <div class="content-add">
          <input 
            v-model="goal.newTodo" 
            placeholder="Neues Todo hinzufügen..."
            @keyup.enter="addTodo(goal)"
            class="content-add-input"
          />
          <button 
            class="content-add-button"
            @click="addTodo(goal)"
            :disabled="!goal.newTodo?.trim()"
          >
            +
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GoalsManager',
  props: {
    branches: {
      type: Array,
      required: true,
      default: () => []
    }
  },
  data() {
    return {
      showGoalForm: false,
      selectedBranchId: '',
      goals: []
    }
  },
  created() {
    // Load goals from localStorage on component mount
    this.goals = JSON.parse(localStorage.getItem('goals') || '[]');
  },
  methods: {
    handleBranchSelect() {
      if (this.selectedBranchId) {
        this.goals.push({
          id: Date.now(),
          branchId: this.selectedBranchId,
          todos: [],
          newTodo: ''
        });
        
        // Save to localStorage
        localStorage.setItem('goals', JSON.stringify(this.goals));
        
        this.showGoalForm = false;
        this.selectedBranchId = '';
      }
    },
    deleteGoal(goalId) {
      this.goals = this.goals.filter(g => g.id !== goalId);
      // Save to localStorage
      localStorage.setItem('goals', JSON.stringify(this.goals));
    },
    addTodo(goal) {
      if (goal.newTodo?.trim()) {
        goal.todos.push({
          id: Date.now(),
          text: goal.newTodo.trim(),
          completed: false
        });
        goal.newTodo = '';
        // Save to localStorage
        localStorage.setItem('goals', JSON.stringify(this.goals));
      }
    },
    deleteTodo(goalId, todoId) {
      const goal = this.goals.find(g => g.id === goalId);
      if (goal) {
        goal.todos = goal.todos.filter(todo => todo.id !== todoId);
        // Save to localStorage
        localStorage.setItem('goals', JSON.stringify(this.goals));
      }
    },
    toggleTodo(goalId, todoId) {
      const goal = this.goals.find(g => g.id === goalId);
      if (goal) {
        const todo = goal.todos.find(t => t.id === todoId);
        if (todo) {
          todo.completed = !todo.completed;
          
          // If todo is marked as completed, create a new version for the next iteration
          if (todo.completed) {
            goal.todos.push({
              id: Date.now(),
              text: todo.text,
              completed: false
            });
          }
          // Save to localStorage
          localStorage.setItem('goals', JSON.stringify(this.goals));
        }
      }
    },
    activeTodos(goal) {
      return goal.todos.filter(todo => !todo.completed);
    },
    completedTodos(goal) {
      return goal.todos.filter(todo => todo.completed);
    },    getBranchColor(branchId) {
      const branch = this.branches.find(b => b.id === branchId);
      return branch ? branch.color : '#ddd';
    },
    getBranchName(branchId) {
      const branch = this.branches.find(b => b.id === branchId);
      return branch ? branch.name : '';
    }
  }
}
</script>

<style scoped>
.goals-manager {
  position: relative;
  height: 100%;
  padding: 0 2rem 2rem 2rem;
}

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
}

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
  position: relative;
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  width: 90%;
  max-width: 300px;
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

.form-group {
  margin-bottom: 0;
}

.branch-select {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
  background-color: white;
}

.goals-container {
  margin-top: 1rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.completed-todos-section {
  margin-top: 1rem;
  padding-top: 1rem;
  border-top: 1px solid rgba(0, 0, 0, 0.1);
}

.content-box {
  padding: 1.5rem;
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  width: 100%;
  min-height: auto;
  transition: transform 0.2s;
}

.content-box:hover {
  transform: scale(1.01);
}

.content-items {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  min-height: 0;
  margin-bottom: 1rem;
}

.content-items:empty {
  margin-bottom: 0;
}

.content-item {
  background-color: rgba(255, 255, 255, 0.9);
  padding: 0.5rem;
  border-radius: 4px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.content-item.completed {
  opacity: 0.7;
}

.content-item-text {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  flex-grow: 1;
}

.content-item-delete {
  background: none;
  border: none;
  color: rgba(0, 0, 0, 0.5);
  cursor: pointer;
  font-size: 1.2rem;
  padding: 0.2rem 0.5rem;
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

.todo-checkbox {
  cursor: pointer;
}
</style>