<script setup>
import { ref, onMounted } from "vue"

const props = defineProps({
  order: Object,
})

const emit = defineEmits(["update:order", "submit"])

const step = ref(1)
const formUser = ref(null)
const shelves = ref([])
const books = ref([])

function setField(key, value) {
  emit("update:order", { ...props.order, [key]: value })
}

async function nextStep1() {
  const response = await formUser.value.validate()
  if (response.valid) step.value = 2
}

function next() { step.value += 1 }
function back() { step.value -= 1 }

onMounted(async () => {
  // Fetch shelves for the dropdown
  const shelvesRes = await fetch('http://localhost:3000/shelves')
  shelves.value = await shelvesRes.json()

  // Fetch books for the dropdown
  const booksRes = await fetch('http://localhost:3000/books')
  books.value = await booksRes.json()
})
</script>

<template>
  <v-stepper v-model="step">
    <v-stepper-header>
      <v-stepper-item :value="1" title="User Name" />
      <v-stepper-item :value="2" title="Select Shelf" />
      <v-stepper-item :value="3" title="Select Book" />
    </v-stepper-header>

    <v-stepper-window>

      <!-- Step 1: User Name -->
      <v-stepper-window-item :value="1">
        <v-form ref="formUser">
          <v-text-field
              :model-value="order.userName"
              @update:model-value="v => setField('userName', v)"
              label="Your Name"
              :rules="[v => !!v || 'Name is required']"
          />
          <v-btn :disabled="!order.userName" @click="nextStep1">Next</v-btn>
        </v-form>
      </v-stepper-window-item>

      <!-- Step 2: Pick a shelf -->
      <v-stepper-window-item :value="2">
        <v-select
            :items="shelves"
            item-title="shelf_name"
            item-value="id"
            :model-value="order.shelfId"
            @update:model-value="v => setField('shelfId', v)"
            label="Select a Shelf"
        />
        <v-btn @click="back">Back</v-btn>
        <v-btn :disabled="!order.shelfId" @click="next">Next</v-btn>
      </v-stepper-window-item>

      <!-- Step 3: Pick a book -->
      <v-stepper-window-item :value="3">
        <v-select
            :items="books"
            item-title="name"
            item-value="id"
            :model-value="order.bookId"
            @update:model-value="v => setField('bookId', v)"
            label="Select a Book"
        />
        <v-btn @click="back">Back</v-btn>
        <v-btn :disabled="!order.bookId" @click="$emit('submit')">
          Review & Submit
        </v-btn>
      </v-stepper-window-item>

    </v-stepper-window>
  </v-stepper>
</template>