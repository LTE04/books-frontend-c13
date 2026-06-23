<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  book: Object
})

const emit = defineEmits(['save'])

const title = ref('')
const author = ref('')
const year = ref('')

watch(
  () => props.book,
  (value) => {
    if (value) {
      title.value = value.title
      author.value = value.author
      year.value = value.year
    }
  },
  { immediate: true }
)

function submit() {
  emit('save', {
    title: title.value,
    author: author.value,
    year: year.value
  })

  title.value = ''
  author.value = ''
  year.value = ''
}
</script>

<template>
  <div>
    <h3>Book Form</h3>

    <input
      v-model="title"
      placeholder="Title"
    />

    <input
      v-model="author"
      placeholder="Author"
    />

    <input
      v-model="year"
      placeholder="Year"
    />

    <button @click="submit">
      Save
    </button>
  </div>
</template>