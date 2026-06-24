<script setup>
import { ref, onMounted } from 'vue'
import api from '../api/client'
import { useAuth } from '../stores/auth'
import BookForm from '../components/BookForm.vue'

const auth = useAuth()

const books = ref([])
const q = ref('')

const showForm = ref(false)
const editingBook = ref(null)
const errorMsg = ref('')

async function load() {
  errorMsg.value = ''
  try {
    const { data } = await api.get('/api/books', {
      params: {
        q: q.value || undefined
      }
    })

    books.value = data.data || data
  } catch (err) {
    console.error(err)
  }
}

async function saved() {
  showForm.value = false
  editingBook.value = null
  await load()
}

function createBook() {
  editingBook.value = null
  showForm.value = true
}

function editBook(book) {
  editingBook.value = book
  showForm.value = true
}

async function deleteBook(id) {
  if (!confirm('Delete this book?')) return

  try {
    await api.delete(`/api/books/${id}`)
    await load()
  } catch (err) {
    alert(err.response?.data?.error || err.message)
  }
}

function canEdit(book) {
  if (!auth.user) return false

  return (
    auth.user.role === 'admin' ||
    auth.user.id === book.user_id
  )
}

function canDelete() {
  return auth.user?.role === 'admin'
}

onMounted(load)
</script>

<template>
  <div>
    <!-- Search + New Book bar -->
    <div class="card" style="display:flex; gap:10px; align-items:center; flex-wrap:wrap; margin-bottom:0;">
      <input
        v-model="q"
        placeholder="Search by title or author"
        style="flex:1; min-width:180px;"
        @keyup.enter="load"
      />
      <button class="primary" @click="load">Search</button>
      <button v-if="auth.isAuthenticated" class="primary" @click="createBook">+ New book</button>
    </div>
 
    <!-- Error -->
    <p v-if="errorMsg" class="alert error">{{ errorMsg }}</p>
 
    <!-- Book Form (inline) -->
    <BookForm
      v-if="showForm"
      :book="editingBook"
      @saved="saved"
      @cancel="showForm = false"
    />
 
    <!-- Book list cards -->
    <div class="card" v-if="books.length">
      <div v-for="book in books" :key="book.id" class="book">
        <div>
          <strong>{{ book.title }}</strong>
          <span class="tag">{{ book.year }}</span>
          <div class="meta">{{ book.author }} • {{ book.genre }}</div>
        </div>
        <div class="actions">
          <button v-if="canEdit(book)" @click="editBook(book)">Edit</button>
          <button v-if="canDelete()" class="danger" @click="deleteBook(book.id)">Delete</button>
        </div>
      </div>
    </div>
 
    <p v-else style="color: var(--muted); text-align:center; margin-top:32px;">
      No books found.
    </p>
  </div>
</template>
 