<template>
  <div class="container mt-4">
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
</template>

<script>
import axios from 'axios';

export default {
  name: 'BookList',
  data() {
    return {
      books: [],
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
        const response = await axios.get('https://tarea4sdbackend.netlify.app/.netlify/functions/books');
        this.books = response.data;
      } catch (error) {
        console.error("Error fetching books:", error);
      }
    },
    async addBook() {
      try {
        const response = await axios.post('https://tarea4sdbackend.netlify.app/.netlify/functions/books', this.bookForm);
        this.books.push(response.data);
        this.clearForm();
      } catch (error) {
        console.error("Error adding book:", error);
      }
    },
    async deleteBook(id) {
      try {
        await axios.delete('https://tarea4sdbackend.netlify.app/.netlify/functions/books', {
          headers: {
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
        await axios.put('https://tarea4sdbackend.netlify.app/.netlify/functions/books', this.bookForm);
        const index = this.books.findIndex(book => book.id === this.bookForm.id);
        if (index !== -1) this.books.splice(index, 1, this.bookForm);
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
    }
  },
  mounted() {
    this.fetchBooks();
  }
};
</script>

<style>
.container {
  background-color: #1e272e;
  padding: 20px;
  border-radius: 10px;
}

h2 {
  color: #ffa502;
}

ul.list-group {
  background-color: #57606f;
  border-radius: 10px;
  padding: 15px;
}

ul.list-group-item {
  background-color: #2f3542;
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
}

.btn-edit {
  background-color: #0984e3;
  color: white;
}

.btn-delete {
  background-color: #d63031;
  color: white;
}

.btn-add {
  background-color: #16a085;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.btn-add:hover {
  background-color: #1abc9c;
}

.form-container {
  background-color: #34495e;
  padding: 15px;
  border-radius: 10px;
  margin-top: 20px;
}

.form-group {
  margin-bottom: 10px;
}

.form-group label {
  color: #f5f6fa;
}

.form-group input {
  width: 100%;
  padding: 8px;
  border-radius: 5px;
  border: 1px solid #7f8c8d;
}

.btn-submit {
  background-color: #f1c40f;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
}

.btn-submit:hover {
  background-color: #f39c12;
}
</style>