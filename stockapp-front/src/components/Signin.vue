<template>
  <div class="container">
    <div class="m-auto border p-5 shadow rounded w-50">
      <h3 class="mb-5">Sign In</h3>
      <form @submit.prevent="signin">
        <div class="text-danger" v-if="error">{{ error }}</div>

        <div class="mb-3 form-inline d-flex justify-content-between">
          <label for="email">E-mail Address</label>
          <input type="email" v-model="email" class="form-control" id="email" placeholder="email@example.com">
        </div>
        <div class="mb-3 form-inline d-flex justify-content-between">
          <label for="password">Password</label>
          <input type="password" v-model="password" class="form-control" id="password" placeholder="Password">
        </div>
        <div class="d-flex justify-content-between">
          <span>
            <button type="submit" class="btn btn-primary">Sign In</button>
          </span>
          <span>
            <router-link to="/signup" class="btn btn-primary">Sign up</router-link>
          </span>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Signin',
  data () {
    return {
      email: '',
      password: '',
      error: ''
    }
  },
  created () {
    this.checkSignedIn()
  },
  updated () {
    this.checkSignedIn()
  },
  methods: {
    signin () {
      this.$http.plain.post('/signin', { email: this.email, password: this.password })
        .then(response => this.signinSuccessful(response))
        .catch(error => this.signinFailed(error))
    },
    signinSuccessful (response) {
      if (!response.data.csrf) {
        this.signinFailed(response)
        return
      }
      localStorage.csrf = response.data.csrf
      localStorage.signedIn = true
      this.error = ''
      this.$router.replace('/records')
    },
    signinFailed (error) {
      this.error = (error.response && error.response.data && error.response.data.error) || ''
      delete localStorage.csrf
      delete localStorage.signedIn
    },
    checkSignedIn () {
      if (localStorage.signedIn) {
        this.$router.replace('/records')
      }
    }
  }
}
</script>
