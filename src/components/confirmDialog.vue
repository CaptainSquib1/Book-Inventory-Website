<script setup>
import { computed } from 'vue'
const props = defineProps({
  open:Boolean,
  order:Object,
  menu: Array,
})

const emit = defineEmits(["update:open","confirm"])

const actionName = computed(() => {
  const found = props.menu.find(m=>m.id === props.order.items)
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
          <v-list-item title="Action:">{{ actionName }}</v-list-item>
          <v-list-item title="Books:">{{ order.books }}</v-list-item>
        </v-list>
      </v-card-text>
      <v-card-actions>
        <v-btn @click="emit('update:open', false)">Cancel</v-btn>
        <v-btn @click="emit('confirm')">Confirm</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>