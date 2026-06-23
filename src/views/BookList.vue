<script setup>
import { ref, onMounted } from 'vue'
import api from '../api/client'
import BookForm from '../components/BookForm.vue'

const books = ref([])
const q = ref('')

const editingBook = ref(null)

async function load() {
  const { data } = await api.get(
    '/api/books',
    {
      params: {
        q: q.value || undefined
      }
    }
  )

  books.value = data.data
}

async function createBook(book) {
  await api.post('/api/books', book)
  await load()
}

async function updateBook(book) {
  await api.put(
    `/api/books/${editingBook.value.id}`,
    book
  )

  editingBook.value = null

  await load()
}

async function deleteBook(id) {
  await api.delete(`/api/books/${id}`)
  await load()
}

function editBook(book) {
  editingBook.value = book
}

onMounted(load)
</script>

<template>
  <div>
    <h2>Books</h2>

    <input
      v-model="q"
      placeholder="Search"
      @keyup.enter="load"
    />

    <button @click="load">
      Search
    </button>

    <BookForm
      v-if="!editingBook"
      @save="createBook"
    />

    <BookForm
      v-else
      :book="editingBook"
      @save="updateBook"
    />

    <ul>
      <li
        v-for="book in books"
        :key="book.id"
      >
        <strong>
          {{ book.title }}
        </strong>

        -
        {{ book.author }}

        ({{ book.year }})

        <button
          @click="editBook(book)"
        >
          Edit
        </button>

        <button
          @click="deleteBook(book.id)"
        >
          Delete
        </button>
      </li>
    </ul>
  </div>
</template>