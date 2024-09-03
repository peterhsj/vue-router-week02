<script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
import { ref } from 'vue'
const tabs = ref([
  { id: '1', title: 'Home', url: '/'},
  { id: '2', title: 'Todo', url: '/todo'},
])
</script>

<template>

  <v-app id="inspire">
    <v-app-bar
      class="px-3"
      density="compact"
      flat
    >
      <v-avatar
        class="hidden-md-and-up"
        color="grey-darken-1"
        size="32"
      ></v-avatar>

      <v-spacer></v-spacer>

      <v-tabs
        color="grey-darken-2"
        centered
      >
        <v-tab
          v-for="tab in tabs"
          :key="tab.id"
          :text="tab.title"
          :to="tab.url"
        ></v-tab>
      </v-tabs>
      <v-spacer></v-spacer>

      <v-avatar
        class="hidden-sm-and-down"
        color="grey-darken-1"
        size="32"
      ></v-avatar>
    </v-app-bar>

    <v-main class="bg-grey-lighten-3">
      <v-container>
        <v-row>
          <v-col
            cols="12"
            lg="8"
            offset-lg="2"
          >
            <v-sheet
              min-height="70vh"
              rounded="lg"
              class="pa-5"
            >
              <RouterView />
            </v-sheet>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>  
</template>
