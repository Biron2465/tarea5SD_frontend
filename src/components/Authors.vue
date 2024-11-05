<template>
  <div class="container mt-4">
    <h2>Authors</h2>
    <ul class="list-group mb-3">
      <li v-for="author in authors" :key="author._id" class="list-group-item">
        <div>
          <h5>{{ author.author }}</h5>
          <small>{{ author.nationality }} - {{ author.fields }}</small>
        </div>
        <div>
          <button class="btn-edit" @click="setEditAuthor(author)">Edit</button>
          <button class="btn-delete" @click="deleteAuthor(author._id)">Delete</button>
        </div>
      </li>
    </ul>
    <button class="btn-add" @click="toggleAddForm">{{ showAddForm ? 'Cancel' : 'Add Author' }}</button>

    <div v-if="showAddForm" class="form-container">
      <h4>{{ editingAuthor ? 'Edit Author' : 'Add Author' }}</h4>
      <form @submit.prevent="editingAuthor ? updateAuthor() : addAuthor()">
        <div class="form-group">
          <label>Author</label>
          <input type="text" v-model="authorForm.author" required>
        </div>
        <div class="form-group">
          <label>Nationality</label>
          <input type="text" v-model="authorForm.nationality" required>
        </div>
        <div class="form-group">
          <label>Birth Year</label>
          <input type="number" v-model="authorForm.birth_year" required>
        </div>
        <div class="form-group">
          <label>Fields</label>
          <input type="text" v-model="authorForm.fields" required>
        </div>
        <button type="submit" class="btn-submit">{{ editingAuthor ? 'Update' : 'Add' }} Author</button>
      </form>
    </div>
  </div>
</template>


<script>
import axios from 'axios';

export default {
  name: 'AuthorList',
  data() {
    return {
      authors: [],
      authorForm: {
        id: null,
        author: '',
        nationality: '',
        birth_year: '',
        fields: ''
      },
      editingAuthor: false,
      showAddForm: false
    };
  },
  methods: {
    async fetchAuthors() {
      try {
        const response = await axios.get('https://tarea4sdbackend.netlify.app/.netlify/functions/authors');
        this.authors = response.data;
      } catch (error) {
        console.error("Error fetching authors:", error);
      }
    },
    async addAuthor() {
      try {
        const response = await axios.post('https://tarea4sdbackend.netlify.app/.netlify/functions/authors', this.authorForm);
        this.authors.push(response.data);
        this.clearForm();
      } catch (error) {
        console.error("Error adding author:", error);
      }
    },
    async deleteAuthor(id) {
      try {
        console.log("Deleting author with ID:", id); // Verifica el ID en la consola
        await axios.delete('https://tarea4sdbackend.netlify.app/.netlify/functions/authors', {
          headers: {
            'Content-Type': 'application/json'
          },
          data: { id } // El ID se envía en el cuerpo
        });
        this.authors = this.authors.filter(author => author._id !== id);
      } catch (error) {
        console.error("Error deleting author:", error);
      }
    },
    setEditAuthor(author) {
      this.authorForm = { ...author };
      this.editingAuthor = true;
      this.showAddForm = true;
    },
    async updateAuthor() {
      try {
        await axios.put('https://tarea4sdbackend.netlify.app/.netlify/functions/authors', {
          id: this.authorForm._id, // Cambia a `id` en el backend y mantén el nombre aquí
          name: this.authorForm.name,
          nationality: this.authorForm.nationality,
          birth_year: this.authorForm.birth_year,
          fields: this.authorForm.fields
        });
        const index = this.authors.findIndex(author => author._id === this.authorForm._id);
        if (index !== -1) this.authors.splice(index, 1, this.authorForm);
        this.clearForm();
      } catch (error) {
        console.error("Error updating author:", error);
      }
    },
    clearForm() {
      this.authorForm = {
        id: null,
        name: '',
        nationality: '',
        birth_year: '',
        fields: ''
      };
      this.editingAuthor = false;
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
    this.fetchAuthors();
  }
};
</script>

<style>
.container {
  background-color: #2c3e50;
  padding: 20px;
  border-radius: 10px;
}

h2 {
  color: #e74c3c;
}

ul.list-group {
  background-color: #34495e;
  border-radius: 10px;
  padding: 15px;
}

ul.list-group-item {
  background-color: #2c3e50;
  color: #ecf0f1;
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
}

.btn-edit {
  background-color: #2980b9;
  color: white;
}

.btn-delete {
  background-color: #c0392b;
  color: white;
}

.btn-add {
  background-color: #27ae60;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.btn-add:hover {
  background-color: #2ecc71;
}

.form-container {
  background-color: #2e4053;
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
  border: 1px solid #7f8c8d;
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