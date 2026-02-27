<script setup>
import {ref, computed, shallowRef} from 'vue'
import home from './home.vue'
import shelves from './shelves.vue'

import {
  useDropZone,
  useMouse,
  useWindowSize,
  useCountdown,
  useDark,
} from '@vueuse/core'

const routes = {
  '/': home,
  '/shelves': shelves,

}

const currentPath = ref(window.location.hash)
const drawer = ref(false);
const rail = ref(false);

window.addEventListener('hashchange', () => {
  currentPath.value = window.location.hash
})

const currentView = computed(() => {
  return routes[currentPath.value.slice(1) || '/'] || NotFound
})

</script>

<template>
  <v-app>
    <v-navigation-drawer v-model="drawer" :rail="rail"
                         permanent
                         @click="rail = false" class="bg-brown text-black">
      <v-list-item prepend-icon="mdi-home" href="#/" title="Home" ></v-list-item>
      <v-list-item prepend-icon="mdi-book" href="#/shelves" title="Book Shelves" ></v-list-item>
    </v-navigation-drawer>
    <v-app-bar color="orange-lighten-3">
      <v-app-bar-nav-icon @click = "drawer = !drawer" icon="mdi-hamburger"></v-app-bar-nav-icon>
      <v-app-bar-title>Book Stand</v-app-bar-title>
    </v-app-bar>
    <v-main class="bg-brown-darken-3">
      <component :is="currentView"></component>

    </v-main>
    <v-footer class="bg-brown-darken-3" app="true">Copyright 2026</v-footer>

  </v-app>
</template>



