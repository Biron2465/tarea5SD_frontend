<template>
  <div class="container mt-4" v-if="isAuthenticated">
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

  <!-- Modal de autenticación para pedir la clave -->
  <div v-else class="auth-modal">
    <h2>Enter Password</h2>
    <input type="password" v-model="password" placeholder="Enter password">
    <button @click="authenticate">Submit</button>
    <p v-if="authError" class="error">Incorrect password. Please try again.</p>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'AuthorList',
  data() {
    return {
      authors: [],
      password: '',       // Clave de autenticación
      authError: false,    // Indica si la autenticación falló
      isAuthenticated: false, // Controla si el usuario está autenticado
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
        const response = await axios.get('https://tarea5sd.netlify.app/.netlify/functions/authors', {
          headers: {
            'X-Password': this.password
          }
        });
        this.authors = response.data;
      } catch (error) {
        console.error("Error fetching authors:", error);
      }
    },
    async addAuthor() {
      try {
        const response = await axios.post('https://tarea5sd.netlify.app/.netlify/functions/authors', this.authorForm, {
          headers: {
            'X-Password': this.password
          }
        });
        this.authors.push(response.data);
        this.clearForm();
      } catch (error) {
        console.error("Error adding author:", error);
      }
    },
    async deleteAuthor(id) {
      try {
        console.log("Deleting author with ID:", id);
        await axios.delete('https://tarea5sd.netlify.app/.netlify/functions/authors', {
          headers: {
            'X-Password': this.password,
            'Content-Type': 'application/json'
          },
          data: { id }
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
        await axios.put('https://tarea5sd.netlify.app/.netlify/functions/authors', {
          id: this.authorForm._id,
          author: this.authorForm.author,
          nationality: this.authorForm.nationality,
          birth_year: this.authorForm.birth_year,
          fields: this.authorForm.fields
        }, {
          headers: {
            'X-Password': this.password
          }
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
        author: '',
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
    },
    async authenticate() {
      try {
        const response = await axios.get('https://tarea5sd.netlify.app/.netlify/functions/authors', {
          headers: {
            'X-Password': this.password
          }
        });
        if (response.status === 200) {
          this.isAuthenticated = true;
          this.authError = false;
          this.fetchAuthors(); // Cargar autores después de la autenticación
        }
      } catch (error) {
        this.authError = true;
        console.error("Authentication failed:", error);
      }
    }
  },
  mounted() {
    this.fetchAuthors();
  }
};
</script>

<style>
/* Estilos */
.auth-modal {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #2c3e50;
  padding: 20px;
  border-radius: 10px;
  margin: 20px auto;
  max-width: 300px;
}

.auth-modal h2 {
  color: #e74c3c;
}

.auth-modal input {
  padding: 10px;
  margin: 10px 0;
  border-radius: 5px;
  border: 1px solid #7f8c8d;
}

.auth-modal button {
  padding: 10px 20px;
  background-color: #27ae60;
  border: none;
  color: white;
  border-radius: 5px;
}

.error {
  color: #e74c3c;
}
</style>
