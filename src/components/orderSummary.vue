<script setup>
import { computed } from "vue"

const props = defineProps({
  order:Object,
  menu:Array
})

const actionName = computed(() => {
  const found = props.menu.find(m=>m.id === props.order.items)
  return found ? found.name : null
})
</script>

<template>
  <v-card>
    <v-card-title>Summary</v-card-title>
    <v-list>
      <v-list-item title="User" :subtitle="order.userName"/>
      <v-list-item title="Action" :subtitle="actionName"/>
      <v-list-item title="Books" :subtitle="order.books"/>
      <v-list-item title="Author" :subtitle="order.author"/>
    </v-list>

  </v-card>
</template>