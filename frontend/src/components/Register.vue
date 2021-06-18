<template>
  <div>
    <h1>Register</h1>
    <v-btn @click="show = 'login'">Login</v-btn>
    <v-btn @click="show = 'index'">Index</v-btn>

    <login v-if="show === 'login'"/>
    <index v-if="show === 'index'" />
  </div>
</template>

<script>
import Login from '@/components/Login'
import Index from '@/components/Index'
export default {
  name: 'register',
  components: {
    Login, Index
  },

  data: () => ({
    show: null
  })
}
</script>
