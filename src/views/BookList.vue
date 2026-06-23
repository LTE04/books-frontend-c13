<script setup> 
import { ref, onMounted } from 'vue'; 
import api from '../api/client'; 
  
const books = ref([]); 
const q = ref(''); 
  
async function load() { 
  const { data } = await api.get('/api/books', { params: { q: q.value || undefined } }); 
  books.value = data.data;
  } 
onMounted(load); 
</script> 
<template> 
<input v-model="q" @keyup.enter="load" placeholder="Search title or author" /> 
<button @click="load">Search</button> 
<ul> 
<li v-for="b in books" :key="b.id"> 
<strong>{{ b.title }}</strong> — {{ b.author }} ({{ b.year }}) 
</li> 
</ul> 
</template> 