<template>
  <div class="container mt-4">
    <h2>Publishers</h2>
    <ul class="list-group mb-3">
      <li v-for="publisher in publishers" :key="publisher.id" class="list-group-item">
        <div>
          <h5>{{ publisher.publisher }}</h5>
          <small>{{ publisher.country }} - Founded: {{ publisher.founded }}</small>
        </div>
        <div>
          <button class="btn-edit" @click="setEditPublisher(publisher)">Edit</button>
          <button class="btn-delete" @click="deletePublisher(publisher.id)">Delete</button>
        </div>
      </li>
    </ul>
    <button class="btn-add" @click="toggleAddForm">{{ showAddForm ? 'Cancel' : 'Add Publisher' }}</button>

    <div v-if="showAddForm" class="form-container">
      <h4>{{ editingPublisher ? 'Edit Publisher' : 'Add Publisher' }}</h4>
      <form @submit.prevent="editingPublisher ? updatePublisher() : addPublisher()">
        <div class="form-group">
          <label>Publisher</label>
          <input type="text" v-model="publisherForm.publisher" required>
        </div>
        <div class="form-group">
          <label>Country</label>
          <input type="text" v-model="publisherForm.country" required>
        </div>
        <div class="form-group">
          <label>Founded Year</label>
          <input type="number" v-model="publisherForm.founded" required>
        </div>
        <div class="form-group">
          <label>Genre</label>
          <input type="text" v-model="publisherForm.genre" required>
        </div>
        <button type="submit" class="btn-submit">{{ editingPublisher ? 'Update' : 'Add' }} Publisher</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'PublisherList',
  data() {
    return {
      publishers: [],
      publisherForm: {
        id: null,
        publisher: '',
        country: '',
        founded: '',
        genre: ''
      },
      editingPublisher: false,
      showAddForm: false
    };
  },
  methods: {
    async fetchPublishers() {
      try {
        const response = await axios.get('https://tarea4sdbackend.netlify.app/.netlify/functions/publishers');
        this.publishers = response.data;
      } catch (error) {
        console.error("Error fetching publishers:", error);
      }
    },
    async addPublisher() {
      try {
        const response = await axios.post('https://tarea4sdbackend.netlify.app/.netlify/functions/publishers', this.publisherForm);
        this.publishers.push(response.data);
        this.clearForm();
      } catch (error) {
        console.error("Error adding publisher:", error);
      }
    },
    async deletePublisher(id) {
      try {
        await axios.delete('https://tarea4sdbackend.netlify.app/.netlify/functions/publishers', {
          headers: {
            'Content-Type': 'application/json'
          },
          data: { id }
        });
        this.publishers = this.publishers.filter(publisher => publisher.id !== id);
      } catch (error) {
        console.error("Error deleting publisher:", error);
      }
    },
    setEditPublisher(publisher) {
      this.publisherForm = { ...publisher };
      this.editingPublisher = true;
      this.showAddForm = true;
    },
    async updatePublisher() {
      try {
        await axios.put('https://tarea4sdbackend.netlify.app/.netlify/functions/publishers', this.publisherForm);
        const index = this.publishers.findIndex(publisher => publisher.id === this.publisherForm.id);
        if (index !== -1) this.publishers.splice(index, 1, this.publisherForm);
        this.clearForm();
      } catch (error) {
        console.error("Error updating publisher:", error);
      }
    },
    clearForm() {
      this.publisherForm = {
        id: null,
        publisher: '',
        country: '',
        founded: '',
        genre: ''
      };
      this.editingPublisher = false;
      this.showAddForm = false;
    },
    toggleAddForm() {
      this.showAddForm = !this.showAddForm;
      if (!this.showAddForm) {
        this.clearForm();
      }
    }
  },
  mounted() {
    this.fetchPublishers();
  }
};
</script>

<style>
.container {
  background-color: #222;
  padding: 20px;
  border-radius: 10px;
}

h2 {
  color: #f39c12;
}

ul.list-group {
  background-color: #444;
  border-radius: 10px;
  padding: 15px;
}

ul.list-group-item {
  background-color: #555;
  color: #fff;
  border: none;
  margin-bottom: 10px;
  border-radius: 8px;
  display: flex;
  justify-content: space-between;
}

.btn-edit,
.btn-delete {
  border: none;
  border-radius: 8px;
  padding: 8px 16px;
  font-size: 14px;
  margin-left: 10px;
}

.btn-edit {
  background-color: #3498db;
  color: white;
}

.btn-delete {
  background-color: #e74c3c;
  color: white;
}

.btn-add {
  background-color: #2ecc71;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.btn-add:hover {
  background-color: #27ae60;
}

.form-container {
  background-color: #333;
  padding: 15px;
  border-radius: 10px;
  margin-top: 20px;
}

.form-group {
  margin-bottom: 10px;
}

.form-group label {
  color: #ecf0f1;
}

.form-group input {
  width: 100%;
  padding: 8px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

.btn-submit {
  background-color: #f39c12;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
}

.btn-submit:hover {
  background-color: #e67e22;
}
</style>