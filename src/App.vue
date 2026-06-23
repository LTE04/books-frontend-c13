<script setup>
import { useAuth } from './stores/auth'

const auth = useAuth()

function logout() {
  auth.logout()
}
</script>

<template>
  <nav>
    <router-link to="/">
      Books
    </router-link>

    |

    <router-link
      v-if="auth.isAuthenticated"
      to="/profile"
    >
      Profile
    </router-link>

    |

    <router-link
      v-if="!auth.isAuthenticated"
      to="/login"
    >
      Login
    </router-link>

    |

    <router-link
      v-if="!auth.isAuthenticated"
      to="/register"
    >
      Register
    </router-link>

    <button
      v-if="auth.isAuthenticated"
      @click="logout"
    >
      Logout
    </button>
  </nav>

  <router-view />
</template>