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
        class="content-box"
        :style="{ backgroundColor: branch.color }"
      >
        <div class="content-box-header">
          <h3>{{ branch.name }}</h3>
          <button class="content-box-delete" @click="deleteBranch(branch.id)">&times;</button>
        </div>
        <div class="content-items">
          <!-- Active Todos -->
          <div v-for="todo in activeTodos(branch)" :key="todo.id" class="content-item">
            <div class="content-item-text">
              <input 
                type="checkbox" 
                :checked="todo.completed"
                @change="toggleTodo(branch.id, todo.id)"
                class="todo-checkbox"
              />
              <span>{{ todo.text }}</span>
            </div>
            <button class="content-item-delete" @click="deleteTodo(branch.id, todo.id)">&times;</button>
          </div>

          <!-- Completed Todos -->
          <div v-if="completedTodos(branch).length > 0" class="completed-todos-section">
            <div v-for="todo in completedTodos(branch)" :key="todo.id" class="content-item completed">
              <div class="content-item-text">
                <input 
                  type="checkbox" 
                  :checked="todo.completed"
                  @change="toggleTodo(branch.id, todo.id)"
                  class="todo-checkbox"
                />
                <span>{{ todo.text }}</span>
              </div>
              <button class="content-item-delete" @click="deleteTodo(branch.id, todo.id)">&times;</button>
            </div>
          </div>
        </div>
        <div class="content-add">
          <input 
            v-model="branch.newTodo" 
            placeholder="Neues Todo hinzufügen..."
            @keyup.enter="addTodo(branch)"
            class="content-add-input"
          />
          <button 
            class="content-add-button"
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
        const newBranch = {
          id: Date.now(),
          name: this.newBranch.name,
          color: this.newBranch.color,
          todos: [],
          newTodo: ''
        };
        
        this.branches.push(newBranch);
        localStorage.setItem('branches', JSON.stringify(this.branches));
        this.$emit('branches-updated'); // Emit the event immediately
        
        // Reset form
        this.newBranch = {
          name: '',
          color: ''
        };
        this.showBranchForm = false;
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

.branches-container {
  margin-top: 2rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
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

.completed-todos-section {
  margin-top: 1rem;
  padding-top: 1rem;
  border-top: 1px solid rgba(0, 0, 0, 0.1);
}

.todo-checkbox {
  cursor: pointer;
}
</style>