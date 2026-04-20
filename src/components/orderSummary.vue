<script setup>
import { computed } from "vue"

const props = defineProps({
  order:Object,
  shelves: Array,
  books: Array,
})

const shelfName = computed(() => {
  const found = props.shelves?.find(s => s.id === props.order.shelfId)
  return found ? found.shelf_name : null
})

const bookName = computed(() => {
  const found = props.books?.find(b => b.id === props.order.bookId)
  return found ? found.name : null
})
const bookAuthor = computed(() => {
  const found = props.books?.find(b => b.id === props.order.bookId)
  return found ? found.author : null
})
</script>

<template>
  <v-card>
    <v-card-title>Summary</v-card-title>
    <v-list>
      <v-list-item title="User" :subtitle="order.userName"/>
      <v-list-item title="Shelf" :subtitle="shelfName"/>
      <v-list-item title="Book" :subtitle="bookName"/>
      <v-list-item title="Author" :subtitle="bookAuthor"/>
    </v-list>

  </v-card>
</template>