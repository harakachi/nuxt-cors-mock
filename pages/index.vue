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

    <div>
      <h1>Please give us your payment details:</h1>
      <card class='stripe-card'
            :class='{ complete }'
            stripe='pk_test_udubtHkBrm0cl996ejNYArfA00lngpPTJ8'
            :options='stripeOptions'
            @change='complete = $event.complete'
      />
      <button class='pay-with-stripe' @click='pay' :disabled='!complete'>Pay with credit card</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import { Card, createToken } from 'vue-stripe-elements-plus'

export default {
  // curl -X POST -d "grant_type=password&client_id=ez6JFeiOjG71dXNLkDdqEvN7ymAoD-KY-yHtf8gOhB4&client_secret=" --verbose http://localhost:3000/api/oauth/token.json

  components: { Card },

  data() {
    return {
      email: '',
      password: '',
      refreshToken: '',
      complete: false,
      client: axios.create({ withCredentials: true }),
      stripeOptions: {
        // see https://stripe.com/docs/stripe.js#element-options for details
      },
    }
  },
  computed: {
    clientId() {
      return 'ouF5dkccsUwrFqLHJ-2JqTOZ_SH2mFBste0mVsu6ePA'
    },
    secret() {
      return 'pjjZFD9e-vQZ3qulQONytVSW13H60U3Ov31Cp0CA4hY'
    }
  },
  methods: {
    async getToken() {
      const res = await this.client.post('http://localhost:3000/api/oauth/token.json', {
        grant_type: 'password',
        client_id: this.clientId,
        client_secret: this.secret,
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
      const res = await this.client.post('http://localhost:3000/api/oauth/token.json', {
        grant_type: 'refresh_token',
        client_id: this.clientId,
        client_secret: this.secret,
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

      const res = await this.client.get('http://localhost:3000/api/users/me.json').catch((error) => (
        console.log(error)
      ))
      console.log(res)
    },

    async pay() {
      const token = await createToken().catch(() => null)
      console.log(token.token.id)
      const res = await this.client.post('http://localhost:3000/api/accounts/credit', {
        token: token.token.id
      }).catch((e) => {
        console.log(e)
      })
      console.log(res)
    }
  }
}
</script>

<style>
  .stripe-card {
    width: 300px;
    border: 1px solid grey;
  }
  .stripe-card.complete {
    border-color: green;
  }
</style>
