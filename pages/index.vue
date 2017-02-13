<template>
  <section class="container">
    <!--<nuxt-link class="button" to="/about">-->
      <!--About page-->
    <!--</nuxt-link>-->
    <!--<hr>-->
    <ul>
      <li v-for="message in messages">
        <strong>Name: </strong> {{ message.name }} <br>
        <strong>Message: </strong> {{ message.text }}
        <hr>
      </li>
    </ul>

    <br><br>
    <form @submit.prevent="postMessage">
      <input type="text" placeholder="Nome" v-model="message.name"><br><br>
      <textarea placeholder="Mensagem" rows="5" v-model="message.text"></textarea><br><br>
      <input type="submit" value="Enviar">
    </form>
  </section>
</template>

<script>
  import axios from 'axios'
  import feathers from 'feathers/client'
  import socketio from 'feathers-socketio/client'
  import io from 'socket.io-client'

  export default {
    data () {
      return axios.get('http://localhost:3030/messages')
        .then((res) => {
          return {
            messages: res.data.data,
            message: {
              name: '',
              text: ''
            }
          }
        })
    },

    methods: {
      postMessage () {
        axios.post('http://localhost:3030/messages', this.message)
          .then((res) => {
            this.message.text = ''
          })
      }
    },

    mounted () {
      const app = feathers().configure(socketio(io('http://localhost:3030')))
      app.service('messages').on('created', (message) => {
        this.messages.push(message)
      })
    }
  }
</script>
