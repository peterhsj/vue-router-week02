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
              v-model="setupField.email"
              density="compact"
              label="E-mail"
              variant="solo"
              hide-details>
            </v-text-field>
          </v-col>
          <v-col cols="12">
            <v-text-field
              type="password"
              v-model="setupField.password"
              density="compact"
              label="Password"
              variant="solo"
              hide-details>
            </v-text-field>
          </v-col>
          <v-col cols="12">
            <v-text-field
              type="text"
              v-model="setupField.nickname"
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
    <v-divider></v-divider>
  </div>

  <div v-if="tokenCookie">
    <h2 class="py-3">代辦事項</h2>
    <div>
      <v-card>
        <v-card-text class="bg-surface-light pt-4">
          <v-container>            
            <v-row>
              <v-col cols="4">
                <v-text-field
                  type="text"
                  v-model="addForm.content"
                  density="compact"
                  placeholder="請輸入代辦事項"
                  variant="solo"
                  hide-details>
                </v-text-field>
              </v-col>
              <v-col cols="2">
                <v-btn
                  type="button"
                  class="text-none"
                  color="indigo-darken-3"
                  variant="flat"
                  @click="addTodo"
                  block
                >新增</v-btn>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <v-data-table
                  :headers="headers"
                  :items="serverItems"
                >
                  <template v-slot:item.actions="{ item }">
                    <v-btn
                      class="me-2"
                      size="small"
                      @click="editItem(item)"
                    >
                      編輯
                    </v-btn>
                    <v-btn
                      size="small"
                      @click="deleteItem(item)"
                    >
                      刪除
                    </v-btn>
                  </template>
                  <template v-slot:no-data>
                    尚無資料
                  </template>
                </v-data-table>
                <v-dialog
                  v-model="dialog"
                  max-width="500px"
                >                      
                  <v-card>
                    <v-card-title>
                      <span class="text-h5">編輯</span>
                    </v-card-title>

                    <v-card-text>
                      <v-container>
                        <v-row>
                          <v-col
                            cols="12"
                          >
                            <v-text-field
                              v-model="editedItem.content"
                              label="待辦事項"
                            ></v-text-field>
                          </v-col>
                        </v-row>
                      </v-container>
                    </v-card-text>

                    <v-card-actions>
                      <v-spacer></v-spacer>
                      <v-btn
                        color="blue-darken-1"
                        variant="text"
                        @click="close"
                      >
                        取消
                      </v-btn>
                      <v-btn
                        color="blue-darken-1"
                        variant="text"
                        @click="save"
                      >
                        儲存
                      </v-btn>
                    </v-card-actions>
                  </v-card>
                </v-dialog>  
                <v-dialog v-model="dialogDelete" max-width="500px">
                    <v-card>
                      <v-card-title class="text-h5">確認刪除 {{ editedItem.content }} 這個項目 ?</v-card-title>
                      <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn color="blue-darken-1" variant="text" @click="closeDelete">取消</v-btn>
                        <v-btn color="blue-darken-1" variant="text" @click="deleteItemConfirm">確認</v-btn>
                        <v-spacer></v-spacer>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
      </v-card>
    </div>
  </div>

</template>

<script setup>
import axios from 'axios';
import { onMounted, ref, nextTick } from "vue";

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
    isRegister.value = false;
    cleanMessage();
  } catch (err) {
    errMessage.value = `${err.response.data.message}`;
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
    errMessage.value = `${err.response.data.message}`;
  }
};

const user = ref({});

// 驗證登入
const checkoutToken = async () => {
  tokenCookie.value = document.cookie.replace(/(?:(?:^|.*;\s*)customTodoToken\s*\=\s*([^;]*).*$)|^.*$/, "$1",);
  const res = await axios.get(`${api}/users/checkout`, {
    headers: {
      Authorization: tokenCookie.value,
    }
  });

  user.value = res.data;
  isLogin.value = false;
  fetchTodo();
};

// 登出
const signOut = async () => {
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

// Add Todo
const addForm = ref({
  content: null
});

const addTodo = async () => {
  const payload = { ...addForm.value  };
  try {
    const res = await axios.post(`${api}/todos`, payload, {
      headers: {
        Authorization: tokenCookie.value,
      }
    });
    fetchTodo();
    addForm.value.content = null;
  } catch (err) {
    console.log({err})
  }
};

// Todo 列表
const headers = ref([
  {
    title: '待辦事項',
    align: 'start',
    sortable: false,
    key: 'content',
  },
  { title: '操作',
    sortable: false,
    key: 'actions',
    align: 'end'
  },
]);
const serverItems = ref([]);
const editedItem = ref({});
const dialog = ref(false);
const dialogDelete = ref(false);

// 取得Todo 列表
const fetchTodo = async () => {
  try {
    const res = await axios.get(`${api}/todos`, {
      headers: {
        Authorization: tokenCookie.value,
      }
    });
    serverItems.value = res.data.data;
  } catch (err) {
    console.log({err})
  }
};

// 編輯 Todo
const editItem = (item) => {
  editedItem.value = {...item};
  dialog.value = true;
};

// 刪除 Todo
const deleteItem = (item) => {
  dialogDelete.value = true;
  editedItem.value = {...item};
};

// 確認刪除
const deleteItemConfirm = async () => {
  const id = editedItem.value.id;
  
  try {
    const res = await axios.delete(`${api}/todos/${id}`,  {
      headers: {
        Authorization: tokenCookie.value,
      }
    });
    fetchTodo();
    closeDelete();  
  } catch (err) {
    console.log({err})
  }
};

// 關閉視窗
const close = () => {
  dialog.value = false;
  nextTick(() => {
    editedItem.value = {};
  })
};

// 關閉刪除視窗
const closeDelete = () => {
  dialogDelete.value = false;
  nextTick(() => {
    editedItem.value = {};
  })
};

const save = async () => {
  const id = editedItem.value.id;
  const payload = { content: editedItem.value.content };
  
  try {
    const res = await axios.put(`${api}/todos/${id}`, payload, {
      headers: {
        Authorization: tokenCookie.value,
      }
    });
    fetchTodo();
    close();  
  } catch (err) {
    console.log({err})
  }

};

onMounted(() => {
  if (tokenCookie.value) {
    checkoutToken();
    fetchTodo();
  }
})

</script>