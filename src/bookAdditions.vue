<script setup>
import {ref, computed, onMounted} from "vue"
import SectionHeader from "./components/sectionHeader.vue"
import AddForm from "./components/orderForm.vue"
import AddSummary from "./components/orderSummary.vue"
import ConfirmDialog from "./components/confirmDialog.vue"

const shelves = ref([])
const books = ref([])

onMounted(async () => {
  const shelvesResponse = await fetch('http://localhost:3000/shelves')
  shelves.value = await shelvesResponse.json()

  const booksResponse = await fetch('http://localhost:3000/books')
  books.value = await booksResponse.json()
})

const order = ref({
  userName: "",
  shelfId: null,
  bookId: null,
})

const dialogOpen = ref(false)
const snackbarOpen = ref(false)
const snackbarText = ref("")

function openConfirm() {
  dialogOpen.value = true
}

async function confirmAddition() {
  try {
    await fetch(`http://localhost:3000/shelves/${order.value.shelfId}/books`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ bookId: order.value.bookId })
    })
    dialogOpen.value = false
    snackbarText.value = "Book Added!"
    snackbarOpen.value = true
    order.value = { userName: "", shelfId: null, bookId: null } // reset form
  } catch (error) {
    snackbarText.value = "Failed to add book!"
    snackbarOpen.value = true
  }
}
</script>

<template>
  <v-container>
    <SectionHeader title="Put a Book on a Shelf" />

    <v-row>
      <v-col cols="12" md="8">
        <AddForm
            v-model:order="order"
            @submit="openConfirm"
        />
      </v-col>

      <v-col cols="12" md="4">
        <AddSummary :order="order" :shelves="shelves" :books="books" />
      </v-col>
    </v-row>

    <ConfirmDialog
        v-model:open="dialogOpen"
        :order="order"
        :shelves="shelves"
        :books="books"
        @confirm="confirmAddition"
    />
  </v-container>
</template>