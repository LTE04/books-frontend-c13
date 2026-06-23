<script setup>
import { onMounted, ref } from 'vue'
import api from '../api/client'

const user = ref(null)

async function loadProfile() {
  try {
    const { data } = await api.get('/auth/me')
    user.value = data
  } catch (err) {
    console.error(err)
  }
}

onMounted(loadProfile)
</script>

<template>
  <div>
    <h2>Profile</h2>

    <div v-if="user">
      <p>
        <strong>Name:</strong>
        {{ user.name }}
      </p>

      <p>
        <strong>Email:</strong>
        {{ user.email }}
      </p>

      <p>
        <strong>Role:</strong>
        {{ user.role }}
      </p>
    </div>
  </div>
</template>