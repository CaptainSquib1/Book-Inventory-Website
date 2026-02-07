<script setup>
import {ref, computed} from 'vue'
import home from './home.vue'
import about from './about.vue'

const routes = {
  '/': home,
  '/about': about
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
                         @click="rail = false">
      <v-list-item prepend-icon="mdi-home" href="#/" title="Home" ></v-list-item>
      <v-list-item prepend-icon="mdi-information-variant-circle-outline" href="#/about" title="About Us" ></v-list-item>
    </v-navigation-drawer>
    <v-app-bar color="orange-lighten-3">
      <v-app-bar-nav-icon @click = "drawer = !drawer" icon="mdi-hamburger"></v-app-bar-nav-icon>
      <v-app-bar-title>Book Stand</v-app-bar-title>
    </v-app-bar>
    <v-main>
      <component :is="currentView"></component>

    </v-main>
    <v-footer app="true">Copyright 2026</v-footer>

  </v-app>
</template>



