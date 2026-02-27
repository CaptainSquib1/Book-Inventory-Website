<script setup>
const newestBooks = [
  {
    title:"Narnia",
    author:"C.S. Lewis",
    description: "Very classic. Super good. Would recommend",
    image: "Newbook.png"
  },
  {
    title:"Sherlock Holmes",
    author:"Arthur Conan Doyle",
    description: "Precise. Super intriguing. Would recommend",
    image: "Newbook.png"
  },
  {
    title:"The Hobbit",
    author:"J.R.R. Tolkien",
    description: "Very hobbitsis. Precious. Would recommend",
    image: "Newbook.png"
  },
  {
    title:"The Space Trilogy",
    author:"C.S. Lewis",
    description: "Very spacy. Little weird. Would recommend",
    image: "Newbook.png"
  },

]
</script>

<template>
  <v-carousel :crossfade="true" :cycle="true" hide-delimiters height="300" :show-arrows="false" :continuous="true">
    <v-carousel-item src="bannerimages1.png" cover></v-carousel-item>
    <v-carousel-item src="bannerimages2.png" cover></v-carousel-item>
    <v-carousel-item src="bannerimages3.png" cover></v-carousel-item>
    <v-carousel-item src="bannerimages4.png" cover></v-carousel-item>
  </v-carousel>

  <v-container class="mt-6">
    <h2 class="text-h5 mb-4">Newest Arrivals</h2>
    <v-row>
      <v-col
          v-for="item in newestBooks" :key="item.title" cols="12" sm="6" md="4">
        <v-card border="sm" class="bg-black text-green-darken-3">
          <v-img :src="item.image" height="180" cover></v-img>
          <v-card-title>{{ item.title }}</v-card-title>
          <v-card-subtitle>{{ item.author}}</v-card-subtitle>
          <v-card-text>{{  item.description }}</v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>

</style>