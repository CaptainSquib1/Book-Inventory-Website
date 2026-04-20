<script setup>
import { computed } from 'vue'
const props = defineProps({
  open:Boolean,
  order:Object,
  shelves: Array,
  books: Array,
})

const emit = defineEmits(["update:open","confirm"])

const shelfName = computed(() => {
  const found = props.shelves?.find(s => s.id === props.order.shelfId)
  return found ? found.shelf_name : null
})

const bookName = computed(() => {
  const found = props.books?.find(b => b.id === props.order.bookId)
  return found ? found.name : null
})
</script>

<template>
  <v-dialog
      :model-value="open"
      @update:model-value="v=>emit('update:open',v)"
  >
    <v-card>
      <v-card-title>Confirm Order</v-card-title>
      <v-card-text>
        <v-list>
          <v-list-item title="Name:">{{ order.userName }}</v-list-item>
          <v-list-item title="Shelf:">{{ shelfName }}</v-list-item>
          <v-list-item title="Book:">{{ bookName }}</v-list-item>
        </v-list>
      </v-card-text>
      <v-card-actions>
        <v-btn @click="emit('update:open', false)">Cancel</v-btn>
        <v-btn @click="emit('confirm')">Confirm</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>