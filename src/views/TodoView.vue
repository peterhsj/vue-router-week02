<template>
  <v-card
    class="mx-auto"
    prepend-icon="$vuetify"
    width="400"
  >
    <template v-slot:title>
      <span class="font-weight-black">Welcome to Todo API</span>
    </template>

    <v-card-text v-if="isLogin" class="bg-surface-light pt-4">
      <v-container v-if="isRegister">
        <v-row align="center" justify="space-between">
          <v-col cols="12" lg="6">
            <h2>註冊</h2>
          </v-col>
          <v-col cols="12" lg="6" class="text-right">
            <v-btn @click="isRegister = false; cleanMessage()" flat>返回登入</v-btn>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12">
            <v-text-field
              type="email"
              v-model="signInField.email"
              density="compact"
              label="E-mail"
              variant="solo"
              hide-details>
            </v-text-field>
          </v-col>
          <v-col cols="12">
            <v-text-field
              type="password"
              v-model="signInField.password"
              density="compact"
              label="Password"
              variant="solo"
              hide-details>
            </v-text-field>
          </v-col>
          <v-col cols="12">
            <v-text-field
              type="text"
              v-model="signInField.nickname"
              density="compact"
              label="Nickname"
              variant="solo"
              hide-details>
            </v-text-field>
          </v-col>
          <v-col cols="12">
            <v-btn
              type="button"
              class="text-none mb-4"
              color="indigo-darken-3"
              size="x-large"
              variant="flat"
              @click="signup"
              block
            >註冊</v-btn>
          </v-col>
        </v-row>
      </v-container>
      <v-container v-else>
        <v-row align="center" justify="space-between">
          <v-col cols="12" lg="6">
            <h2>登入</h2>
          </v-col>
          <v-col cols="12" lg="6" class="text-right">
            <v-btn @click="isRegister = true; cleanMessage()" flat>我要註冊</v-btn>
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12">
            <v-text-field
              type="email"
              v-model="signInField.email"
              density="compact"
              label="E-mail"
              variant="solo"
              hide-details>
            </v-text-field>
          </v-col>
          <v-col cols="12">
            <v-text-field
              type="text"
              v-model="signInField.password"
              density="compact"
              label="Password"
              variant="solo"
              hide-details>
            </v-text-field>
          </v-col>
          <v-col cols="12">
            <v-btn
              type="button"
              class="text-none mb-4"
              color="indigo-darken-3"
              size="x-large"
              variant="flat"
              @click="signin"
              block
            >登入</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-card-text>
    <v-card-text v-else class="bg-surface-light pt-4">
      <v-btn type="button" color="indigo-darken-3" @click="signOut" block>登出</v-btn>
    </v-card-text>
  </v-card>

  <v-alert
    v-if="errMessage"
    :text="errMessage"
    title="錯誤訊息："
    density="compact"
    type="warning"
    width="400"
    class="mx-auto my-3"
  ></v-alert>
  
  <div v-if="user.uid" class="text-center my-5">
    <h2>{{ user.nickname }}，歡迎光臨</h2>    
  </div>

  <v-divider></v-divider>


</template>

<script setup>
import axios from 'axios';
import { onMounted, ref } from "vue";

const api = 'https://todolist-api.hexschool.io';
const isRegister = ref(false);
const errMessage = ref('');
const tokenCookie = ref('');
const isLogin = ref(true);

const cleanMessage = () => {
  errMessage.value = '';
};

// 註冊
const setupField = ref({
  email: '',
  password: '',
  nickname: '',
});
const uid = ref('');

const signup = async () => {
  try {
    const res = await axios.post(`${api}/users/sign_up`, setupField.value);
    uid.value = res.data.uid;
    cleanMessage();
  } catch (err) {
    errMessage.value = `${err.response.data.message.join('，')}`;
  }
};

// 登入
const signInField = ref({
  email: '',
  password: '',
});
const token = ref('');

const signin = async () => {
  try {
    const res = await axios.post(`${api}/users/sign_in`, signInField.value);
    // API 的 exp 時間是錯的，改用日期加一天
    token.value = res.data.token;
    document.cookie = `customTodoToken=${res.data.token}; expires=${new Date() + 1}; path=/`;
    // 清空錯誤訊息
    cleanMessage();
    // 驗證登入
    checkoutToken();
  } catch (err) {
    errMessage.value = `${err.response.data.message.join('，')}`;
  }
};

const user = ref({});

// 驗證登入
const checkoutToken = async () => {
  tokenCookie.value = document.cookie.replace(/(?:(?:^|.*;\s*)customTodoToken\s*\=\s*([^;]*).*$)|^.*$/, "$1",);
  console.log('登入', tokenCookie.value);
  const res = await axios.get(`${api}/users/checkout`, {
    headers: {
      Authorization: tokenCookie.value,
    }
  });

  user.value = res.data;
  isLogin.value = false;
};

// 登出
const signOut = async () => {
  tokenCookie.value = document.cookie.replace(/(?:(?:^|.*;\s*)customTodoToken\s*\=\s*([^;]*).*$)|^.*$/, "$1",);
  console.log('登出', tokenCookie.value);
  try {
  const res = await axios.post(`${api}/users/sign_out`,{}, {
    headers: {
      Authorization: tokenCookie.value,
    }
  });
  user.value = {};
  isLogin.value = res.data.status;
  document.cookie = "customTodoToken=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;";
  } catch (err) {
    console.log({err})
  }
};


onMounted(() => {
  checkoutToken();
})

</script>