<template>
  <div class="calendar">
    <div class="calendar-grid">
      <div class="weekday-header" v-for="day in weekDays" :key="day">
        {{ day }}
      </div>
      <div
        v-for="(day, index) in calendarDays"
        :key="index"
        class="calendar-day"
        :class="{ 
          'current-day': isCurrentDay(day),
          'past-date': isPastDate(day)
        }"
        @click="selectDay(day)"
      >
        <span class="day-number">{{ day ? day.getDate() : '' }}</span>
        <div v-if="getEvents(day).length" class="events-list">
          <div v-for="event in getEvents(day)" :key="event.id" class="event-item">
            {{ event.name }}
          </div>
        </div>
      </div>
    </div>

    <!-- Popup with Events and Notes -->
    <div v-if="showPopup && selectedDate" class="popup-overlay" @click.self="closePopup">
      <div class="popup-content">
        <div class="popup-header">
          <h2>{{ formatDate }}</h2>
          <button class="close-button" @click="closePopup">&times;</button>
        </div>
        <div class="popup-body">
          <!-- Events Section -->
          <div class="section">
            <h3 class="section-title">Events</h3>
            <input 
              v-model="newEvent.name"
              placeholder="Event hinzufügen"
              class="event-input"
              @keyup.enter="saveEvent"
            />
            <div class="events-list-popup">
              <div v-for="event in currentEvents" :key="event.id" class="event-item-popup">
                <span>{{ event.name }}</span>
                <button class="delete-event" @click="deleteEvent(event.id)">&times;</button>
              </div>
            </div>
          </div>

          <!-- Notes Section -->
          <div class="section">
            <h3 class="section-title">Notizen</h3>
            <textarea
              v-model="newNote"
              placeholder="Notizen hinzufügen"
              class="notes-input"
              @blur="saveNote"
            ></textarea>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MonthCalendar',
  data() {
    return {
      weekDays: ['Mo', 'Di', 'Mi', 'Do', 'Fr', 'Sa', 'So'],
      showPopup: false,
      selectedDate: null,
      events: JSON.parse(localStorage.getItem('calendar_events') || '{}'),
      notes: JSON.parse(localStorage.getItem('calendar_notes') || '{}'),
      newEvent: {
        name: ''
      },
      newNote: ''
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
    },
    currentNote() {
      if (!this.selectedDate) return '';
      const dateKey = this.selectedDate.toISOString().split('T')[0];
      return this.notes[dateKey] || '';
    },
    calendarDays() {
      const today = new Date();
      const year = today.getFullYear();
      const month = today.getMonth();
      
      // Get first day of the month
      const firstDay = new Date(year, month, 1);
      
      // Get the Monday of the first week
      let firstMonday = new Date(firstDay);
      const dayOfWeek = firstMonday.getDay();
      // Adjust for Sunday being 0 in JavaScript
      const daysToSubtract = dayOfWeek === 0 ? 6 : dayOfWeek - 1;
      firstMonday.setDate(firstMonday.getDate() - daysToSubtract);

      // Create array for 5 weeks (35 days)
      const days = [];
      for (let i = 0; i < 35; i++) {
        const currentDay = new Date(firstMonday);
        currentDay.setDate(firstMonday.getDate() + i);
        days.push(currentDay);
      }

      return days;
    },
    currentEvents() {
      if (!this.selectedDate) return [];
      const dateKey = this.selectedDate.toISOString().split('T')[0];
      return this.events[dateKey] || [];
    }
  },
  methods: {
    isCurrentDay(day) {
      if (!day) return false;
      const today = new Date();
      return day.getDate() === today.getDate() &&
             day.getMonth() === today.getMonth() &&
             day.getFullYear() === today.getFullYear();
    },
    
    hasEvents(day) {
      if (!day) return false;
      const dateKey = day.toISOString().split('T')[0];
      return this.events[dateKey] && this.events[dateKey].length > 0;
    },

    hasNotes(day) {
      if (!day) return false;
      const dateKey = day.toISOString().split('T')[0];
      return !!this.notes[dateKey];
    },

    getEvents(day) {
      if (!day) return [];
      const dateKey = day.toISOString().split('T')[0];
      return this.events[dateKey] || [];
    },
    
    selectDay(day) {
      if (!day) return;
      this.selectedDate = day;
      this.showPopup = true;
      this.newEvent.name = '';
      const dateKey = day.toISOString().split('T')[0];
      this.newNote = this.notes[dateKey] || '';
    },
    
    closePopup() {
      this.showPopup = false;
      this.selectedDate = null;
      this.newEvent.name = '';
      this.newNote = '';
    },
    
    saveEvent() {
      if (!this.selectedDate || !this.newEvent.name) return;
      
      const dateKey = this.selectedDate.toISOString().split('T')[0];
      if (!this.events[dateKey]) {
        this.events[dateKey] = [];
      }
      
      this.events[dateKey].push({
        id: Date.now(),
        name: this.newEvent.name
      });
      
      // Save to localStorage
      localStorage.setItem('calendar_events', JSON.stringify(this.events));
      
      this.newEvent.name = '';
    },

    saveNote() {
      if (!this.selectedDate) return;
      
      const dateKey = this.selectedDate.toISOString().split('T')[0];
      if (this.newNote) {
        this.notes[dateKey] = this.newNote;
      } else {
        delete this.notes[dateKey];
      }

      // Save to localStorage
      localStorage.setItem('calendar_notes', JSON.stringify(this.notes));
    },
    
    deleteEvent(eventId) {
      if (!this.selectedDate) return;
      const dateKey = this.selectedDate.toISOString().split('T')[0];
      if (this.events[dateKey]) {
        this.events[dateKey] = this.events[dateKey].filter(event => event.id !== eventId);
        if (this.events[dateKey].length === 0) {
          delete this.events[dateKey];
        }
        // Save to localStorage
        localStorage.setItem('calendar_events', JSON.stringify(this.events));
      }
    },

    isPastDate(day) {
      if (!day) return false;
      const today = new Date();
      today.setHours(0, 0, 0, 0);
      return day < today;
    }
  }
}
</script>

<style scoped>
.calendar {
  padding: 1rem;
}

.calendar-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 4px;
}

.weekday-header {
  text-align: center;
  font-weight: 500;
  padding: 0.5rem;
  color: #666;
}

.calendar-day {
  aspect-ratio: 1;
  border: 1px solid #eee;
  padding: 0.5rem;
  position: relative;
  cursor: pointer;
  background: white;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  min-height: 80px;
  transition: border 0.2s ease;
}

.calendar-day:hover {
  border: 1px solid #333;
}

.day-number {
  font-size: 0.9rem;
  color: #333;
  align-self: flex-end;
  margin-bottom: 4px;
}

.current-day {
  background: #f0f7ff;
}

.current-day .day-number {
  color: #2196F3;
  font-weight: bold;
}

.past-date {
  background: #fafafa;
}

.past-date .day-number {
  color: #ccc;
}

.events-list {
  width: 100%;
  font-size: 0.8rem;
  overflow: hidden;
}

.event-item {
  padding: 2px 4px;
  margin: 2px 0;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  color: #333;
  background-color: #e3f2fd;
  border-radius: 4px;
}

.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.2);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.popup-content {
  background: white;
  padding: 1.5rem;
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

.popup-header h2 {
  font-size: 1.1rem;
  font-weight: 500;
  color: #333;
  margin: 0;
}

.section {
  margin-bottom: 1.5rem;
}

.section-title {
  font-size: 1rem;
  font-weight: 500;
  color: #666;
  margin-bottom: 0.5rem;
}

.close-button {
  background: none;
  border: none;
  font-size: 1.5rem;
  cursor: pointer;
  color: #666;
  padding: 0;
}

.event-input {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #eee;
  border-radius: 4px;
  margin-bottom: 0.5rem;
}

.notes-input {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #eee;
  border-radius: 4px;
  min-height: 100px;
  resize: vertical;
}

.events-list-popup {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.event-item-popup {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem;
  background: #e3f2fd;
  border-radius: 4px;
}

.delete-event {
  background: none;
  border: none;
  color: #999;
  cursor: pointer;
  padding: 0 0.5rem;
}

.delete-event:hover {
  color: #ff4444;
}
</style>