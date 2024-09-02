<template>
  <h1>Todo API</h1>
  <div>
    <input type="email" placeholder="Email" v-model="setupField.email">
    <input type="text" placeholder="Password" v-model="setupField.password">
    <input type="text" placeholder="nickname"  v-model="setupField.nickname">
    <button type="button" @click="signup">註冊</button>
    <br />
    UID : {{ UID }}
    <br />
    {{ setupField }}
  </div>
</template>

<script setup>
import axios from 'axios';
import { ref } from "vue";

const api = 'https://todolist-api.hexschool.io';
const setupField = ref({
  email: '',
  password: '',
  nickname: '',
});
const UID = ref('');

const signup = async () => {
  try {
    const res = await axios.post(`${api}/users/sign_up`, setupField.value);
    UID.value = res.data.uid; 

  } catch (err) {
    console.error('err', err.response.data.message)
  }
};


</script>