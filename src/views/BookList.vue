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

async function load() {
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

    <h2>Books</h2>

    <div style="margin-bottom:20px;">
      <input
        v-model="q"
        placeholder="Search title or author"
        @keyup.enter="load"
      />

      <button @click="load">
        Search
      </button>

      <button
        v-if="auth.isAuthenticated"
        @click="createBook"
      >
        + New Book
      </button>
    </div>

    <BookForm
      v-if="showForm"
      :book="editingBook"
      @saved="saved"
      @cancel="showForm = false"
    />

    <table border="1" cellpadding="10">
      <thead>
        <tr>
          <th>Title</th>
          <th>Author</th>
          <th>Year</th>
          <th>Genre</th>
          <th>Actions</th>
        </tr>
      </thead>

      <tbody>
        <tr
          v-for="book in books"
          :key="book.id"
        >
          <td>{{ book.title }}</td>
          <td>{{ book.author }}</td>
          <td>{{ book.year }}</td>
          <td>{{ book.genre }}</td>

          <td>

            <button
              v-if="canEdit(book)"
              @click="editBook(book)"
            >
              Edit
            </button>

            <button
              v-if="canDelete()"
              @click="deleteBook(book.id)"
            >
              Delete
            </button>

          </td>
        </tr>
      </tbody>
    </table>

  </div>
</template>