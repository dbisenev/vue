<template>
  <div class="menu" v-if="isVisible">
    <ul>
      <li @click="filterByTopic('All')">All Persons</li>
      <li v-for="topic in availableTopics" :key="topic" @click="filterByTopic(topic)">{{ topic }}</li>
    </ul>

    <div class="contacts">
      <h2>Contact Us</h2>
      <p><strong>Phone:</strong> +123 456 789</p>
      <p><strong>Address:</strong> Green st.</p>
      <p><strong>Email:</strong> email@email.com</p>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    isVisible: {
      type: Boolean,
      required: true
    },
    availableTopics: {
      type: Array,
      required: true
    }
  },
  methods: {
    filterByTopic(topic) {
      this.$emit('filter-topic', topic); // Emit event to parent
    }
  }
};
</script>

<style scoped>
.menu {
  width: 250px;
  position: absolute;
  height: 100vh;
  transition: transform 0.3s ease-in-out;
  z-index:1;
  overflow-y: auto;
  border: 1px solid #e6f2e6;
}


.menu ul {
  list-style-type: none;
  padding: 0;
}

.menu li {
  padding: 10px 15px;
  cursor: pointer;
  color: #333;
  border-bottom: 1px solid #f0f0f0;
}

.menu li:hover {
  background-color: #7ed56f;
  color: white;
}

.contacts {
  margin-top: 100px;
  background-color: #f0f8e2;
  padding: 20px;
  border-radius: 5px;
  border: 1px solid #42b983;
}
</style>
