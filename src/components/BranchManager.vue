<template>
  <div class="branch-manager">
    <!-- Add Branch Button -->
    <button class="add-button" @click="showBranchForm = true">+</button>

    <!-- Branch Form Popup -->
    <div v-if="showBranchForm" class="popup-overlay" @click.self="showBranchForm = false">
      <div class="popup-content">
        <div class="popup-header">
          <h2>Neue Branch</h2>
          <button class="close-button" @click="showBranchForm = false">&times;</button>
        </div>
        <div class="form-content">
          <div class="form-group">
            <input 
              v-model="newBranch.name" 
              placeholder="Branch Name" 
              class="branch-input"
            />
          </div>
          <div class="form-group">
            <div class="color-options">
              <div 
                v-for="color in availableColors" 
                :key="color"
                class="color-option"
                :class="{ selected: newBranch.color === color }"
                :style="{ backgroundColor: color }"
                @click="newBranch.color = color"
              ></div>
            </div>
          </div>
          <button 
            class="save-button" 
            @click="createBranch"
            :disabled="!isValidBranch"
          >
            Erstellen
          </button>
        </div>
      </div>
    </div>

    <!-- Branches Display -->
    <div class="branches-container">
      <div 
        v-for="branch in branches" 
        :key="branch.id"
        class="branch-box"
        :style="{ backgroundColor: branch.color }"
      >
        <div class="branch-header">
          <h3>{{ branch.name }}</h3>
          <button class="delete-branch-button" @click="deleteBranch(branch.id)">&times;</button>
        </div>
        <div class="todos-container">
          <!-- Active Todos -->
          <div v-for="todo in activeTodos(branch)" :key="todo.id" class="todo-item">
            <div class="todo-content">
              <input 
                type="checkbox" 
                :checked="todo.completed"
                @change="toggleTodo(branch.id, todo.id)"
                class="todo-checkbox"
              />
              <span>{{ todo.text }}</span>
            </div>
            <button class="delete-todo" @click="deleteTodo(branch.id, todo.id)">&times;</button>
          </div>

          <!-- Completed Todos -->
          <div v-if="completedTodos(branch).length > 0" class="completed-todos-section">
            <div v-for="todo in completedTodos(branch)" :key="todo.id" class="todo-item completed">
              <div class="todo-content">
                <input 
                  type="checkbox" 
                  :checked="todo.completed"
                  @change="toggleTodo(branch.id, todo.id)"
                  class="todo-checkbox"
                />
                <span>{{ todo.text }}</span>
              </div>
              <button class="delete-todo" @click="deleteTodo(branch.id, todo.id)">&times;</button>
            </div>
          </div>
        </div>
        <div class="add-todo-container">
          <input 
            v-model="branch.newTodo" 
            placeholder="Neues Todo hinzufügen..."
            @keyup.enter="addTodo(branch)"
            class="todo-input"
          />
          <button 
            class="add-todo-button"
            @click="addTodo(branch)"
            :disabled="!branch.newTodo?.trim()"
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
  name: 'BranchManager',
  data() {
    return {
      showBranchForm: false,
      branches: JSON.parse(localStorage.getItem('branches') || '[]'),
      selectedBranch: null,
      newBranch: {
        name: '',
        color: ''
      },
      availableColors: [
        '#FF9AA2', // Light Red
        '#FFB7B2', // Light Pink
        '#FFDAC1', // Light Orange
        '#E2F0CB', // Light Green
        '#B5EAD7', // Mint
        '#C7CEEA', // Light Blue
        '#9FB7E0', // Blue
        '#D4A5A5'  // Mauve
      ]
    }
  },
  computed: {
    isValidBranch() {
      return this.newBranch.name.trim() && this.newBranch.color;
    }
  },
  methods: {
    createBranch() {
      if (this.isValidBranch) {
        this.branches.push({
          id: Date.now(),
          name: this.newBranch.name.trim(),
          color: this.newBranch.color,
          todos: [],
          newTodo: ''
        });
        this.newBranch = { name: '', color: '' };
        this.showBranchForm = false;
        this.$emit('branches-updated');
        // Save to localStorage
        localStorage.setItem('branches', JSON.stringify(this.branches));
      }
    },
    addTodo(branch) {
      if (branch.newTodo?.trim()) {
        branch.todos.push({
          id: Date.now(),
          text: branch.newTodo.trim(),
          completed: false
        });
        branch.newTodo = '';
        // Save to localStorage
        localStorage.setItem('branches', JSON.stringify(this.branches));
      }
    },
    deleteTodo(branchId, todoId) {
      const branch = this.branches.find(b => b.id === branchId);
      if (branch) {
        branch.todos = branch.todos.filter(todo => todo.id !== todoId);
        // Save to localStorage
        localStorage.setItem('branches', JSON.stringify(this.branches));
      }
    },
    toggleTodo(branchId, todoId) {
      const branch = this.branches.find(b => b.id === branchId);
      if (branch) {
        const todo = branch.todos.find(t => t.id === todoId);
        if (todo) {
          todo.completed = !todo.completed;
          // Save to localStorage
          localStorage.setItem('branches', JSON.stringify(this.branches));
        }
      }
    },
    activeTodos(branch) {
      return branch.todos.filter(todo => !todo.completed);
    },
    completedTodos(branch) {
      return branch.todos.filter(todo => todo.completed);
    },
    deleteBranch(branchId) {
      this.branches = this.branches.filter(b => b.id !== branchId);
      this.$emit('branches-updated');
      // Save to localStorage
      localStorage.setItem('branches', JSON.stringify(this.branches));
    },
    selectBranch(branchId) {
      this.selectedBranch = branchId;
      this.$emit('branch-selected', branchId);
    }
  }
}
</script>

<style scoped>
.branch-manager {
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
  background-color: white;
  padding: 2rem;
  border-radius: 8px;
  width: 90%;
  max-width: 400px;
}

.form-group {
  margin-bottom: 1.5rem;
}

.branch-input {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
}

.color-options {
  display: flex;
  gap: 0.5rem;
  flex-wrap: wrap;
}

.color-option {
  width: 2rem;
  height: 2rem;
  border-radius: 4px;
  cursor: pointer;
  transition: transform 0.2s;
}

.color-option:hover {
  transform: scale(1.1);
}

.color-option.selected {
  outline: 2px solid #007bff;
  outline-offset: 2px;
}

.save-button {
  width: 100%;
  padding: 0.75rem;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1rem;
}

.save-button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.branches-container {
  margin-top: 2rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.branch-box {
  padding: 1.5rem;
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  width: 100%;
  min-height: auto;
}

.branch-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.branch-header h3 {
  margin: 0;
  color: rgba(0, 0, 0, 0.8);
}

.delete-branch-button {
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

.branch-box:hover .delete-branch-button {
  opacity: 1;
}

.delete-branch-button:hover {
  background-color: rgba(0, 0, 0, 0.1);
}

.todos-container {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  min-height: 0;
  margin-bottom: 1rem;
}

.todos-container:empty {
  margin-bottom: 0;
}

.todo-item {
  background-color: rgba(255, 255, 255, 0.9);
  padding: 0.5rem;
  border-radius: 4px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.todo-content {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  flex-grow: 1;
}

.completed-todos-section {
  margin-top: 1rem;
  padding-top: 1rem;
  border-top: 1px solid rgba(0, 0, 0, 0.1);
}

.todo-item.completed .todo-content span {
  text-decoration: line-through;
  color: #666;
}

.delete-todo {
  background: none;
  border: none;
  color: #666;
  cursor: pointer;
  font-size: 1.2rem;
  padding: 0 0.3rem;
}

.add-todo-container {
  margin-top: auto;
  display: flex;
  gap: 0.5rem;
}

.todo-input {
  flex-grow: 1;
  padding: 0.5rem;
  border: none;
  border-radius: 4px;
  background-color: rgba(255, 255, 255, 0.9);
}

.add-todo-button {
  background-color: rgba(255, 255, 255, 0.9);
  border: none;
  border-radius: 4px;
  width: 2rem;
  cursor: pointer;
  font-size: 1.2rem;
  color: #666;
}
</style>