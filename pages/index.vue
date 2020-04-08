<template>
  <div>
    email: <input type="text" v-model="email">
    password: <input type="text" v-model="password">
    <button @click="getToken()">getToken</button>
    <br>
    refresh token: {{refreshToken}}
    <br>
    <button @click="getUser()">getUser</button>
    <button @click="getRefreshToken()">refresh!</button>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  // curl -X POST -d "grant_type=password&client_id=ez6JFeiOjG71dXNLkDdqEvN7ymAoD-KY-yHtf8gOhB4&client_secret=" --verbose http://localhost:3000/api/oauth/token.json
  data() {
    return {
      email: '',
      password: '',
      refreshToken: ''
    }
  },
  methods: {
    async getToken() {
      const client = axios.create({ withCredentials: true })
      const res = await client.post('http://localhost:3000/api/oauth/token.json', {
        grant_type: 'password',
        client_id: 'ez6JFeiOjG71dXNLkDdqEvN7ymAoD-KY-yHtf8gOhB4',
        client_secret: 'kYs0wzSxEZTjTQe3UtdXq-yp3IAJS-T29cwQhF5lrWI',
        username: this.email,
        password: this.password
      }).catch((error) => {
        console.log(error)
      })
      if (res) {
        this.refreshToken = res.data.refresh_token
      }
    },

    async getRefreshToken() {
      const client = axios.create({ withCredentials: true })
      const res = await client.post('http://localhost:3000/api/oauth/token.json', {
        grant_type: 'refresh_token',
        client_id: 'ez6JFeiOjG71dXNLkDdqEvN7ymAoD-KY-yHtf8gOhB4',
        client_secret: 'kYs0wzSxEZTjTQe3UtdXq-yp3IAJS-T29cwQhF5lrWI',
        refresh_token: this.refreshToken
      }).catch((error) => {
        console.log(error)
      })
      console.log(res)
      if (res) {
        this.refreshToken = res.data.refresh_token
      }
    },

    async getUser() {
      const client = axios.create({ withCredentials: true })
      const res = await client.get('http://localhost:3000/api/users/me.json').catch((error) => (
        console.log(error)
      ))
      console.log(res)
    },
  }
}
</script>

<style>
</style>
