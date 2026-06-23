<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { useAuth } from '../stores/auth'

const auth = useAuth()
const router = useRouter()

const name = ref('')
const email = ref('')
const password = ref('')
const error = ref('')

async function submit() {
  try {
    await auth.register(
      name.value,
      email.value,
      password.value
    )

    router.push('/')
  } catch (e) {
    error.value =
      e.response?.data?.error ||
      e.message
  }
}
</script>

<template>
  <div>
    <h2>Register</h2>

    <p v-if="error" style="color:red">
      {{ error }}
    </p>

    <input
      v-model="name"
      placeholder="Name"
    />

    <input
      v-model="email"
      placeholder="Email"
    />

    <input
      v-model="password"
      type="password"
      placeholder="Password"
    />

    <button @click="submit">
      Register
    </button>
  </div>
</template>