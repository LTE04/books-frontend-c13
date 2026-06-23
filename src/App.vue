<script setup>
import { useAuth } from './stores/auth'
import { useRouter } from 'vue-router'

const auth = useAuth()
const router = useRouter()

function logout() {
  auth.logout()
  router.push('/login')
}
</script>

<template>

  <header>

    <h1>UTM Books</h1>

    <nav>

      <RouterLink to="/">
        Books
      </RouterLink>

      |

      <RouterLink
        v-if="auth.isAuthenticated"
        to="/profile"
      >
        Profile
      </RouterLink>

      |

      <RouterLink
        v-if="!auth.isAuthenticated"
        to="/login"
      >
        Login
      </RouterLink>

      |

      <RouterLink
        v-if="!auth.isAuthenticated"
        to="/register"
      >
        Register
      </RouterLink>

      |

      <button
        v-if="auth.isAuthenticated"
        @click="logout"
      >
        Logout
      </button>

    </nav>

    <p v-if="auth.user">
      Welcome,
      <strong>{{ auth.user.name }}</strong>
      ({{ auth.user.role }})
    </p>

  </header>

  <hr>

  <router-view />

</template>