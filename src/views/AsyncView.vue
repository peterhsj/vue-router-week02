<template>
  <h1>Async Function</h1>
</template>

<script setup>
// Async Await 使用前提，必須搭配 Promise
// 1. setTimeout -> callback
// 2. jquery Ajax -> cakkback
// 3. Fetch API -> promise
// 4. axios

import axios from 'axios';
console.log(axios);

//method: get post delete put patch
axios.get('https://randomuser.me/api/')
  .then(response => {
    console.log({response});
    return response.data;
  })




</script>