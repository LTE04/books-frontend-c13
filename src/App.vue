<script setup>
import { useAuth } from './stores/auth';
import { RouterLink, RouterView, useRouter } from 'vue-router';

const auth = useAuth()
const router = useRouter()

const apiBase = import.meta.env.VITE_API_BASE_URL;

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
      Welcome, <strong>{{ auth.user.name }}</strong>
      <span class="tag" :class="{ admin: auth.isAdmin }">{{ auth.user.role }}</span>
    </p>
  </header>

  <hr>

  <main>
    <RouterView />
    <p style="text-align:center; color: var(--muted, #999); font-size: 11px; margin-top: 32px;">
      API: {{ apiBase }}
    </p>
  </main>

</template>