<template>
  <div>
    email: <input type="text" v-model="email">
    password: <input type="text" v-model="password">
    <button @click="getToken()">getToken</button>
    <br>
    <br>
    <button @click="getUser()">getUser</button>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  // curl -X POST -d "grant_type=password&client_id=ez6JFeiOjG71dXNLkDdqEvN7ymAoD-KY-yHtf8gOhB4&client_secret=" --verbose http://localhost:3000/api/oauth/token.json
  data() {
    return {
      email: '',
      password: ''
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
      console.log(res)
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
