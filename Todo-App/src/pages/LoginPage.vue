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


export default {
  data () {
    return {
       baseURL: 'https://vue-todos.robertmckenney.ca/api',
      loginName: '',
      password: '',
      errorMessage: '',
      isWorking: false
    }
  },
  methods: {
    login () {
       this.errorMessage = ''
  this.isWorking = true
      // send credentials to the OAuth2 server
      axios.post('https://vue-todos.robertmckenney.ca/oauth/token',
  {
    grant_type: 'password',
    client_id: '3',
    client_secret: 'TJJiU0ULORMxmpToXaTX4J4qo1cttVJ3nGSkg1xk',
    username: this.loginName,
    password: this.password,
    scope: '*'
  }
)


      // get back an access_token
.then(response => {
    // eslint-disable-next-line
    const { expires_in, ...rest } = response.data
    const apiTokens = {
      expires_at: moment().add(expires_in, 'seconds').format(),
      ...rest
    }
    this.$emit('saveApiTokens', apiTokens)
    this.loginName = null
    this.password = null
  })
.catch(error => this.handleError(error))
  .finally(() => { this.isWorking = false })
      // save the token




      // redirect to the main application view
    },

handleError (error) {
  if (error.response) {
    // The request was made and the server responded with a status code
    // that falls out of the range of 2xx
    switch (error.response.status) {
      case 400: {
        this.errorMessage = 'Both login name and password are required.'
        break
      }
      case 401: {
        this.errorMessage = 'Incorrect username or password'
        break
      }
      default:
        this.errorMessage = 'Sorry. There was an error processing your request.'
    }
  } else if (error.request) {
    // The request was made but no response was received
    console.log(error.request)
  } else {
    // Something happened in setting up the request that triggered an Error
    console.log('Error', error.message)
  }
  console.log(error.config)
}
}
}

</script>
