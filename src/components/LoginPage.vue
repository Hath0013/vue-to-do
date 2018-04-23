<template>
  <form class="form" @submit.prevent="login">
    <h1 class="title">Vue Todos</h1>
    <div v-if="errorMessage" class="errors">{{ errorMessage }}</div>
    <div class="form-group">
      <label for="email">Login name</label>
      <input
        type="email"
        id="email"
        v-model="loginName"
        placeholder="email"
        autocomplete="username"
        required
      >
    </div>
    <div class="form-group">
      <label for="password">Password</label>
      <input
        type="password"
        id="password"
        v-model="password"
        autocomplete="current-password"
        required
      >
    </div>
    <button type="submit" @click="login">
      {{ isWorking ? 'Working ...' : 'LOGIN' }}
    </button>
  </form>
</template>


<script>
import moment from 'moment'

axios.post('https://vue-todos.robertmckenney.ca/oauth/token',
  {
    grant_type: 'password',
    client_id: '2',
    client_secret: 'TJJiU0ULORMxmpToXaTX4J4qo1cttVJ3nGSkg1xk',
    username: this.loginName,
    password: this.password,
    scope: '*'
  }
),
.then(response => {
  this.$emit('saveApiTokens', response.data)
  this.loginName = null
  this.password = null
}),


export default {
  data () {
    return {
      loginName: '',
      password: '',
      errorMessage: '',
      isWorking: false
    }
  },
  methods: {
    login () {
      // send credentials to the OAuth2 server
      // get back an access_token
      // save the token
      // redirect to the main application view
    },
    handleError () {
      // we will create this later
    }
  }
}
</script>
