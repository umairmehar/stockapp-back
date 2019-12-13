<template>
  <div class="container">
    <div class="m-auto border p-5 shadow rounded w-50">
      <h3 class="mb-5">Sign Up</h3>
      <form @submit.prevent="signup">
        <div class="text-dangrr" v-if="error">{{ error }}</div>

        <div class="mb-5 form-inline d-flex justify-content-between">
          <label for="email" class="label">E-mail Address</label>
          <input type="email" v-model="email" class="form-control" id="email" placeholder="andy@web-crunch.com">
        </div>

        <div class="mb-5 form-inline d-flex justify-content-between">
          <label for="password" class="label">Password</label>
          <input type="password" v-model="password" class="form-control" id="password" placeholder="Password">
        </div>

        <div class="mb-5 form-inline d-flex justify-content-between">
          <label for="password_confirmation" class="label">Password Confirmation</label>
          <input type="password" v-model="password_confirmation" class="form-control" id="password_confirmation" placeholder="Password Confirmation">
        </div>
        <div class="d-flex justify-content-around">
          <span>
            <button type="submit" class="btn btn-primary">Sign Up</button>
          </span>
          <span>
            <router-link to="/" class="btn btn-primary">Sign In</router-link>
          </span>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Signup',
  data () {
    return {
      email: '',
      password: '',
      password_confirmation: '',
      error: ''
    }
  },
  created () {
    this.checkedSignedIn()
  },
  updated () {
    this.checkedSignedIn()
  },
  methods: {
    signup () {
      this.$http.plain.post('/signup', { email: this.email, password: this.password, password_confirmation: this.password_confirmation })
        .then(response => this.signupSuccessful(response))
        .catch(error => this.signupFailed(error))
    },
    signupSuccessful (response) {
      if (!response.data.csrf) {
        this.signupFailed(response)
        return
      }

      localStorage.csrf = response.data.csrf
      localStorage.signedIn = true
      this.error = ''
      this.$router.replace('/records')
    },
    signupFailed (error) {
      this.error = (error.response && error.response.data && error.response.data.error) || 'Something went wrong'
      delete localStorage.csrf
      delete localStorage.signedIn
    },
    checkedSignedIn () {
      if (localStorage.signedIn) {
        this.$router.replace('/records')
      }
    }
  }
}
</script>
