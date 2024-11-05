<template>
  <div class="container mt-4" v-if="isAuthenticated">
    <h2>Books</h2>
    <ul class="list-group mb-3">
      <li v-for="book in books" :key="book.id" class="list-group-item">
        <div>
          <h5>{{ book.title }}</h5>
          <small>{{ book.author }} - {{ book.language }}</small>
        </div>
        <div>
          <button class="btn-edit" @click="setEditBook(book)">Edit</button>
          <button class="btn-delete" @click="deleteBook(book.id)">Delete</button>
        </div>
      </li>
    </ul>
    <button class="btn-add" @click="toggleAddForm">{{ showAddForm ? 'Cancel' : 'Add Book' }}</button>

    <div v-if="showAddForm" class="form-container">
      <h4>{{ editingBook ? 'Edit Book' : 'Add Book' }}</h4>
      <form @submit.prevent="editingBook ? updateBook() : addBook()">
        <!-- Formulario de agregar/editar libro -->
        <div class="form-group">
          <label>Title</label>
          <input type="text" v-model="bookForm.title" required>
        </div>
        <div class="form-group">
          <label>Edition</label>
          <input type="text" v-model="bookForm.edition" required>
        </div>
        <div class="form-group">
          <label>Copyright Year</label>
          <input type="number" v-model="bookForm.copyright" required>
        </div>
        <div class="form-group">
          <label>Language</label>
          <input type="text" v-model="bookForm.language" required>
        </div>
        <div class="form-group">
          <label>Pages</label>
          <input type="number" v-model="bookForm.pages" required>
        </div>
        <div class="form-group">
          <label>Author</label>
          <input type="text" v-model="bookForm.author" required>
        </div>
        <button type="submit" class="btn-submit">{{ editingBook ? 'Update' : 'Add' }} Book</button>
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
  name: 'BookList',
  data() {
    return {
      books: [],
      password: '',
      authError: false,
      isAuthenticated: false,
      bookForm: {
        id: null,
        title: '',
        edition: '',
        copyright: '',
        language: '',
        pages: '',
        author: '',
        author_id: '',
        publisher: '',
        publisher_id: ''
      },
      editingBook: false,
      showAddForm: false
    };
  },
  methods: {
    async fetchBooks() {
      try {
        const response = await axios.get('https://tarea5sd.netlify.app/.netlify/functions/books', {
          headers: {
            'X-Password': this.password
          }
        });
        this.books = response.data;
      } catch (error) {
        console.error("Error fetching books:", error);
      }
    },
    async addBook() {
      try {
        console.log("Adding book:", this.bookForm); // Verificar si el método se llama
        const response = await axios.post('https://tarea5sd.netlify.app/.netlify/functions/books', this.bookForm, {
          headers: {
            'X-Password': this.password
          }
        });
        console.log("Book created:", response.data);
        this.books.push(response.data);
        this.clearForm();
      } catch (error) {
        console.error("Error adding book:", error);
      }
    },
    async deleteBook(id) {
      try {
        await axios.delete('https://tarea5sd.netlify.app/.netlify/functions/books', {
          headers: {
            'X-Password': this.password,
            'Content-Type': 'application/json'
          },
          data: { id }
        });
        this.books = this.books.filter(book => book.id !== id);
      } catch (error) {
        console.error("Error deleting book:", error);
      }
    },
    setEditBook(book) {
      this.bookForm = { ...book };
      this.editingBook = true;
      this.showAddForm = true;
    },
    async updateBook() {
      try {
        console.log("Updating book:", this.bookForm); // Verificar si el método se llama
        const response = await axios.put('https://tarea5sd.netlify.app/.netlify/functions/books', this.bookForm, {
          headers: {
            'X-Password': this.password
          }
        });
        console.log("Book updated:", response.data);
        const index = this.books.findIndex(book => book.id === this.bookForm.id);
        if (index !== -1) this.books.splice(index, 1, response.data);
        this.clearForm();
      } catch (error) {
        console.error("Error updating book:", error);
      }
    },
    clearForm() {
      this.bookForm = {
        id: null,
        title: '',
        edition: '',
        copyright: '',
        language: '',
        pages: '',
        author: '',
        author_id: '',
        publisher: '',
        publisher_id: ''
      };
      this.editingBook = false;
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
        const response = await axios.get('https://tarea5sd.netlify.app/.netlify/functions/books', {
          headers: {
            'X-Password': this.password
          }
        });
        if (response.status === 200) {
          this.isAuthenticated = true;
          this.authError = false;
          this.fetchBooks();
        }
      } catch (error) {
        this.authError = true;
        console.error("Authentication failed:", error);
      }
    }
  }
};
</script>

<style>
/* Estilos */
.auth-modal {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #2f3542;
  padding: 20px;
  border-radius: 10px;
  margin: 20px auto;
  max-width: 300px;
}

.auth-modal h2 {
  color: #ffa502;
}

.auth-modal input {
  padding: 10px;
  margin: 10px 0;
  border-radius: 5px;
  border: 1px solid #7f8c8d;
}

.auth-modal button {
  padding: 10px 20px;
  background-color: #1abc9c;
  border: none;
  color: white;
  border-radius: 5px;
}

.error {
  color: #e74c3c;
}
</style>
