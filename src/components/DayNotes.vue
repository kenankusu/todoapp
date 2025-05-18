<template>
  <div v-if="show" class="popup-overlay" @click.self="close">
    <div class="popup-content">
      <div class="popup-header">
        <h2>{{ formatDate }}</h2>
        <button class="close-button" @click="close">&times;</button>
      </div>
      <div class="popup-body">
        <textarea
          v-model="noteText"
          placeholder="Schreibe hier deine Notizen..."
        ></textarea>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DayNotes',
  props: {
    show: {
      type: Boolean,
      required: true
    },
    selectedDate: {
      type: Date,
      required: true
    },
    initialNote: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      noteText: this.initialNote
    }
  },
  computed: {
    formatDate() {
      if (!this.selectedDate) return '';
      return this.selectedDate.toLocaleDateString('de-DE', {
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric'
      });
    }
  },
  watch: {
    initialNote(newValue) {
      this.noteText = newValue;
    }
  },
  methods: {
    close() {
      this.$emit('close');
    },
    updateNote() {
      this.$emit('update:note', {
        date: this.selectedDate.toISOString().split('T')[0],
        text: this.noteText
      });
    }
  }
}
</script>

<style scoped>
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
  max-width: 500px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.popup-body textarea {
  width: 100%;
  min-height: 200px;
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
  resize: vertical;
}
</style>