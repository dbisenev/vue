<template>
  <div id="app">
    <div class="container">
      <!-- Button to toggle the menu visibility -->
      <button class="menu-toggle" @click="toggleMenu">
        {{ isMenuVisible ? 'Hide Menu' : 'Show Menu' }}
      </button>

      <!-- Left Menu (collapsible) -->
      <transition name="slide">
        <div class="menu" v-if="isMenuVisible">
          <ul>
            <li @click="filterByTopic('All')">All Persons</li>
            <li v-for="topic in availableTopics" :key="topic" @click="filterByTopic(topic)">{{ topic }}</li>
          </ul>
        </div>
      </transition>

      <!-- Content (Cards) -->
      <div class="content">
        <h1>Person Cards</h1>
        <!-- Buttons to sort by date and rating -->
        <button @click="sortByDate">Sort by Date</button>
        <button @click="sortByRating">Sort by Rating</button>

        <div class="card-container">
          <!-- Cards for each person -->
          <div class="card" v-for="person in paginatedPersons" :key="person.id">
            <img :src="person.Avatar" alt="avatar" class="avatar" />
            <div class="card-info">
              <h3>{{ person.PersonName }}</h3>
              <p><strong>Topic:</strong> {{ person.Topic }}</p>
              <p><strong>Date:</strong> {{ person.PubDate }}</p>
              <p><strong>Rating:</strong> {{ person.Rating }}</p>
              <p><strong>Commentary:</strong> {{ person.Commentary }}</p>
            </div>
          </div>
        </div>

        <div class="pagination-buttons">
          <button @click="prevPage" :disabled="currentPage === 1">Предыдущее</button>
          <button @click="nextPage" :disabled="currentPage === totalPages">Далее</button>
        </div>

      </div>
    </div>
  </div>
</template>

<script>


export default {
  data() {
    return {
      persons: [
        { id: 1, PersonName: 'John Doe', Avatar: 'https://i.pravatar.cc/150?img=1', PubDate: '2023-09-20', Rating: 4.5, Commentary: 'Great work!', Topic: 'Vue.js Development' },
        { id: 2, PersonName: 'Jane Smith', Avatar: 'https://i.pravatar.cc/150?img=2', PubDate: '2023-09-18', Rating: 3.8, Commentary: 'Very helpful!', Topic: 'JavaScript' },
        { id: 3, PersonName: 'Samuel Johnson', Avatar: 'https://i.pravatar.cc/150?img=3', PubDate: '2023-09-19', Rating: 4.2, Commentary: 'Insightful work!', Topic: 'Web Development' },
        { id: 4, PersonName: 'Emily Turner', Avatar: 'https://i.pravatar.cc/150?img=4', PubDate: '2023-09-17', Rating: 4.9, Commentary: 'Absolutely amazing!', Topic: 'CSS' },
        { id: 5, PersonName: 'James Brown', Avatar: 'https://i.pravatar.cc/150?img=5', PubDate: '2023-09-16', Rating: 4.7, Commentary: 'Superb job!', Topic: 'Vue.js Development' },
        { id: 6, PersonName: 'Nancy Green', Avatar: 'https://i.pravatar.cc/150?img=6', PubDate: '2023-09-15', Rating: 4.3, Commentary: 'Very informative!', Topic: 'JavaScript' },
        { id: 7, PersonName: 'Tom Lee', Avatar: 'https://i.pravatar.cc/150?img=7', PubDate: '2023-09-14', Rating: 3.9, Commentary: 'Good insight!', Topic: 'Web Development' },
        { id: 8, PersonName: 'Lucy White', Avatar: 'https://i.pravatar.cc/150?img=8', PubDate: '2023-09-13', Rating: 4.6, Commentary: 'Amazing work!', Topic: 'CSS' },
        { id: 9, PersonName: 'Michael Clark', Avatar: 'https://i.pravatar.cc/150?img=9', PubDate: '2023-09-12', Rating: 4.4, Commentary: 'Well done!', Topic: 'Vue.js Development' },
        { id: 10, PersonName: 'Sara Wilson', Avatar: 'https://i.pravatar.cc/150?img=10', PubDate: '2023-09-11', Rating: 4.8, Commentary: 'Excellent!', Topic: 'JavaScript' },
        { id: 11, PersonName: 'Anna Belle', Avatar: 'https://i.pravatar.cc/150?img=11', PubDate: '2023-09-10', Rating: 4.1, Commentary: 'Great!', Topic: 'HTML' },
        { id: 12, PersonName: 'David Black', Avatar: 'https://i.pravatar.cc/150?img=12', PubDate: '2023-09-09', Rating: 3.7, Commentary: 'Needs improvement.', Topic: 'CSS' },
        { id: 13, PersonName: 'Chloe Green', Avatar: 'https://i.pravatar.cc/150?img=13', PubDate: '2023-09-08', Rating: 4.5, Commentary: 'Well executed!', Topic: 'JavaScript' },
        { id: 14, PersonName: 'Henry Ford', Avatar: 'https://i.pravatar.cc/150?img=14', PubDate: '2023-09-07', Rating: 4.2, Commentary: 'Very useful.', Topic: 'Vue.js Development' },
        { id: 15, PersonName: 'Emma Stone', Avatar: 'https://i.pravatar.cc/150?img=15', PubDate: '2023-09-06', Rating: 4.6, Commentary: 'Fantastic job!', Topic: 'Web Development' },
        { id: 16, PersonName: 'Jack Sparrow', Avatar: 'https://i.pravatar.cc/150?img=16', PubDate: '2023-09-05', Rating: 4.3, Commentary: 'Helpful insights.', Topic: 'JavaScript' },
        { id: 17, PersonName: 'Lara Croft', Avatar: 'https://i.pravatar.cc/150?img=17', PubDate: '2023-09-04', Rating: 4.1, Commentary: 'Incredible!', Topic: 'CSS' },
        { id: 18, PersonName: 'Bruce Wayne', Avatar: 'https://i.pravatar.cc/150?img=18', PubDate: '2023-09-03', Rating: 4.8, Commentary: 'Great work!', Topic: 'HTML' },
        { id: 19, PersonName: 'Clark Kent', Avatar: 'https://i.pravatar.cc/150?img=19', PubDate: '2023-09-02', Rating: 4.0, Commentary: 'Nice presentation.', Topic: 'Vue.js Development' },
        { id: 20, PersonName: 'Peter Parker', Avatar: 'https://i.pravatar.cc/150?img=20', PubDate: '2023-09-01', Rating: 4.4, Commentary: 'Very interesting.', Topic: 'Web Development' },
        // Continue adding data up to 20 persons
      ],
      selectedTopic: 'All',
      isMenuVisible: false, // Controls the visibility of the menu
      currentPage: 1,
      perPage: 4,
    };
  },

  computed:{
    totalPages(){
          return Math.ceil(this.persons.length / this.perPage);
    },
    paginatedPersons(){
        const start = (this.currentPage - 1) * this.perPage;
        const end = start + this.perPage;
        return this.filteredPersons.slice(start, end);
    },
    availableTopics() {
      // Извлечение уникальных тем
      const topics = this.persons.map(person => person.Topic);
      return [...new Set(topics)]; // Удаляем дубликаты
    },
    filteredPersons() {
      if (this.selectedTopic === 'All') {
        return this.persons;
      } else {
        return this.persons.filter(person => person.Topic === this.selectedTopic);
      }
    }
  },
  methods: {
    sortByDate() {
      this.filteredPersons.sort((a, b) => new Date(b.PubDate) - new Date(a.PubDate));
    },
    sortByRating() {
      this.filteredPersons.sort((a, b) => b.Rating - a.Rating);
    },
    filterByTopic(topic) {
      this.selectedTopic = topic;
      this.currentPage = 1
    },
    toggleMenu() {
      this.isMenuVisible = !this.isMenuVisible; // Toggle menu visibility on button click
    },
    prevPage(){
      if(this.currentPage > 1){
        this.currentPage--;
      }
    },
    nextPage(){
      if (this.currentPage < this.totalPages){
        this.currentPage++;
      }
    },

  }
};
</script>

<style>
/* Container to hold the sidebar and content */
.container {
  display: flex;
  flex-direction: row;
}

.menu-toggle {
  position: fixed;
  top: 20px;
  left: 20px;
  padding: 10px 20px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  z-index: 10; /* Make sure the button is above the menu */
}

.menu-toggle:hover {
  background-color: #1aab73;
}

/* Slide transition for the menu */
.slide-enter-active, .slide-leave-active {
  transition: all 0.5s ease;
}

.slide-enter {
  transform: translateX(-100%);
}

.slide-leave-to {
  transform: translateX(-100%);
}

.menu {
  width: 200px;
  padding: 10px;
  background-color: #ffffff;
  position: fixed;
  top: 70px;
  left: 0;
  height: 100vh;
  overflow-y: auto;
  border-right: 1px solid #ffffff;
  z-index: 1; /* Keep the menu below the button */
}

.menu h2 {
  font-size: 18px;
  margin-bottom: 15px;
}

.menu ul {
  list-style-type: none;
  padding: 0;
}

.menu li {
  padding: 10px;
  cursor: pointer;
  color: #333;
  border-bottom: 1px solid #ccc;
}

.menu li:hover {
  background-color: #ddd;
}

.content {
  margin-left: 220px;
  padding: 20px;
}

.card-container {
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
  gap: 20px;
  margin-bottom: 20px;
}

.card {
  width: 200px;
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 10px;
  text-align: center;
}

.avatar {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  object-fit: cover;
}

.card-info {
  margin-top: 10px;
}

button {
  margin: 15px;
  padding: 10px 20px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #1aab73;
}
</style>
