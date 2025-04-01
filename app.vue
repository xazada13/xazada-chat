<script setup>
import { io } from "socket.io-client";

const messages = ref([])

const username = ref('')

const connect = ref(false)

const text = ref('')

let socket = null

function handleConnect() {
  if (username.value.length > 0) {

    connect.value = true;

    socket = io('ws://localhost:3000');

    socket.on('chat message', (data) => {

      messages.value.push({ text: data.text, username: data.username })

    })

  }

}


function sendMessage() {

  socket.emit('chat message', { username: username.value, text: text.value })

  text.value = ''

}
</script>
<template>
  <form @submit.prevent="handleConnect()" class="form">
    <input v-if="!connect" type="text" v-model="username" placeholder="Ваше имя">
    <button v-if="!connect" type="submit">Войти в чат</button>
  </form>
  <form @submit.prevent="sendMessage()" class="form">
    <textarea v-if="connect" v-model="text" placeholder="Ваше сообщение..."></textarea>
    <button v-if="connect" type="submit">Отправить</button>
  </form>
  <section v-if="connect">
    <div v-for="message in messages">
      <h1>{{ message.username }}</h1>
      <p>{{ message.text }}</p>
    </div>
  </section>
</template>

<style>
.form {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
</style>
