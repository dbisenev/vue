

<template>
  <div id="app">
    <header class="main-header">
      <h1>New courses on Fall season!</h1>
      <p>Full details on our Instagram</p>
    </header>

    <div class="container">
      <button class="menu-toggle" @click="toggleMenu">
        {{ isMenuVisible ? 'Hide Menu' : 'Show Menu' }}
      </button>

      <!-- Подключение компонента SidebarMenu -->
      <SidebarMenu
          :is-visible="isMenuVisible"
          :availableTopics="availableTopics"
          @filter-topic="filterByTopic"
      />

      <div class="content">
        <h1>Person Cards</h1>

        <select id="optionSelect" v-model="sortOption" @change="applySort">
          <option value="date">Date</option>
          <option value="rating">Rating</option>
        </select>

        <div class="card-container">
          <div class="card" v-for="person in paginatedPersons" :key="person.id">
            <img :src="person.Avatar" alt="avatar" class="avatar" />
            <div class="card-info">
              <h3>{{ person.PersonName }}</h3>
              <p><strong>Topic:</strong> {{ person.Topic }}</p>
              <p><strong>Date:</strong> {{ person.PubDate }}</p>
              <p><strong>Rating:</strong></p>
              <star-rating :rating="person.Rating"></star-rating>
              <p><strong>Commentary:</strong> {{ person.Commentary }}</p>
              <button class="like-button" @click="likePerson(person)">Like</button>
            </div>
          </div>
        </div>

        <div class="page-info">
          <p>{{ currentPage }}/{{ totalPages }}</p>
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
import SidebarMenu from './SidebarMenu.vue';
import _ from 'lodash';
import starRating from "@/starRating.vue";

export default {
  components: {
    starRating,
    SidebarMenu
  },
  data() {
    return {
      persons: [
        // Список данных о людях
        { id: 1, PersonName: 'John Doe', Avatar: 'https://i.pravatar.cc/150?img=1', PubDate: '2023-09-20', Rating: 1  , Commentary: 'Great work!', Topic: 'Vue.js Development' },
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

      ],
      selectedTopic: 'All',
      isMenuVisible: false,
      currentPage: 1,
      perPage: 4,
      sortOption: 'date'
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.filteredPersons.length / this.perPage);
    },
    paginatedPersons() {
      const start = (this.currentPage - 1) * this.perPage;
      const end = start + this.perPage;
      return this.sortedPersons.slice(start, end);
    },
    availableTopics() {
      const topics = this.persons.map(person => person.Topic);
      return [...new Set(topics)];
    },
    filteredPersons() {
      if (this.selectedTopic === 'All') {
        return this.persons;
      } else {
        return this.persons.filter(person => person.Topic === this.selectedTopic);
      }
    },
    sortedPersons() {
      if (this.sortOption === 'date') {
        return _.orderBy(this.filteredPersons, ['PubDate'], ['desc']);
      } else if (this.sortOption === 'rating') {
        return _.orderBy(this.filteredPersons, ['Rating'], ['desc']);
      }
      return this.filteredPersons;
    }
  },
  methods: {
    applySort() {
      this.currentPage = 1;
    },
    filterByTopic(topic) {
      this.selectedTopic = topic;
      this.currentPage = 1;
    },
    toggleMenu() {
      this.isMenuVisible = !this.isMenuVisible;
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage--;
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    likePerson(person) {
      person.Rating = (person.Rating + 0.1);
    }
  }
};
</script>
<style>

.main-header {
  background-color: #7ed56f;
  color: white;
  text-align: center;
  padding: 20px 0;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  font-family: 'Arial', sans-serif;
}

.main-header h1 {
  font-size: 28px;
  margin: 0;
  padding: 5px 0;
  font-weight: bold;
}

.main-header p {
  font-size: 16px;
  margin: 0;
  font-style: italic;
}

.container {
  display: flex;
  flex-direction: row;
  background-color: #f9fdf9;
  min-height: 100vh;
  font-family: 'Arial', sans-serif;
}

.menu-toggle {
  position: absolute;
  top: 20px;
  left: 20px;
  padding: 10px 20px;
  background-color: #5baa4e;
  color: white;
  border: none;
  border-radius: 30px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.menu-toggle:hover {
  background-color: #6ac65e;
}

.content {
  margin-left: 260px;
  padding: 20px;
  flex-grow: 1;
}


.card-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 20px;
}

.card {
  width: 250px;
  background-color: white;
  border-radius: 15px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  transition: transform 0.3s ease;
}

.card:hover {
  transform: translateY(-10px);
}

.card img {
  width: 100%;
  height: 150px;
  object-fit: cover;
}

.card-info {
  padding: 15px;
  text-align: center;
}

.card-info h3 {
  font-size: 18px;
  margin-bottom: 10px;
  color: #333;
}

.card-info p {
  font-size: 14px;
  margin-bottom: 5px;
  color: #666;
}

.card-info strong {
  color: #7ed56f;
}


.pagination-buttons {
  margin-top: 20px;
}

.pagination-buttons button {
  background-color: #7ed56f;
  color: white;
  border: none;
  padding: 10px 20px;
  margin: 0 5px;
  border-radius: 30px;
  font-size: 14px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.pagination-buttons button:disabled {
  background-color: #d4e8d4;
  cursor: not-allowed;
}

.pagination-buttons button:not(:disabled):hover {
  background-color: #6ac65e;
}

select {
  padding: 10px;
  border: 1px solid #e6f2e6;
  border-radius: 30px;
  background-color: white;
  color: #333;
  font-size: 14px;
  margin: 10px 0;
  transition: border-color 0.3s ease;
}

select:focus {
  outline: none;
  border-color: #7ed56f;
}

.like-button {
  background-color: #7ed56f;
  color: white;
  border: none;
  padding: 8px 16px;
  font-size: 14px;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 10px;
}

.like-button:hover {
  background-color: #66cc55;
}

.contacts h2 {
  margin-bottom: 15px;
}

</style>
