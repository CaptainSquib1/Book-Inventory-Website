<script setup>
import {onMounted, ref} from "vue";

const books = ref([])

onMounted(async () => {

  const booksResponse = await fetch('/api/books')
  books.value = await booksResponse.json()

})
</script>

<template>
  <v-carousel :crossfade="true" :cycle="true" hide-delimiters height="300" :show-arrows="false" :continuous="true">
    <v-carousel-item src="bannerimages1.png" cover></v-carousel-item>
    <v-carousel-item src="bannerimages2.png" cover></v-carousel-item>
    <v-carousel-item src="bannerimages3.png" cover></v-carousel-item>
    <v-carousel-item src="bannerimages4.png" cover></v-carousel-item>
  </v-carousel>

  <v-container class="mt-6">
    <h2 class="text-h5 mb-4">Books</h2>
    <v-row>
      <v-col v-for="item in books" :key="item.id" cols="12" sm="6" md="4">
        <v-card border="sm" class="bg-black text-green-darken-3">
          <v-img :src="item.image || 'default.png'" height="180" cover></v-img>
          <v-card-title>{{ item.name }}</v-card-title>
          <v-card-subtitle>{{ item.author}}</v-card-subtitle>
          <v-card-text>{{  item.description }}</v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>

</style>