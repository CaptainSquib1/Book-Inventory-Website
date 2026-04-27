<script setup>
import {ref, onMounted} from "vue"
import SectionHeader from "./components/sectionHeader.vue"
import AddForm from "./components/orderForm.vue"
import AddSummary from "./components/orderSummary.vue"
import ConfirmDialog from "./components/confirmDialog.vue"

const shelves = ref([])
const books = ref([])

onMounted(async () => {
  const shelvesResponse = await fetch('http://172.238.188.82:3000/shelves')
  shelves.value = await shelvesResponse.json()

  const booksResponse = await fetch('http://172.238.188.82:3000/books')
  books.value = await booksResponse.json()
})
async function fetchData() {
  const shelvesResponse = await fetch('http://172.238.188.82:3000/shelves')
  shelves.value = await shelvesResponse.json()

  const booksResponse = await fetch('http://172.238.188.82:3000/books')
  books.value = await booksResponse.json()
}

const emptyOrder = {
  userName: "",
  process: null,
  shelfId: null,
  bookId: null,
  bookName: "",
  bookAuthor: "",
  bookDescription: "",
  bookGenre: "",
  bookImage: "",
}

const order = ref({ ...emptyOrder })

const dialogOpen = ref(false)
const snackbarOpen = ref(false)
const snackbarText = ref("")

function openConfirm() {
  dialogOpen.value = true
}

async function confirmAddition() {
  try {
    let response
    if (order.value.process === "placeBook") {
      response = await fetch(`http://172.238.188.82:3000/shelves/`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ shelfId: order.value.shelfId, bookId: order.value.bookId })
      })
    }

    else if (order.value.process === "unplaceBook") {
      response = await fetch(`http://172.238.188.82:3000/shelves/`, {
        method: 'DELETE',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ shelfId: order.value.shelfId, bookId: order.value.bookId })
      })
    }

    else if (order.value.process === "addBook") {
      response = await fetch(`http://172.238.188.82:3000/books/`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          bookName: order.value.bookName,
          author: order.value.bookAuthor,
          description: order.value.bookDescription,
          genre: order.value.bookGenre,
          image: order.value.bookImage,
        })
      })
    }
    if (response.ok) {
      const successMessages = {
        placeBook: "Book placed on shelf!",
        unplaceBook: "Book removed from shelf!",
        addBook: "Book added to library!",
      }
      snackbarText.value = successMessages[order.value.process]
      dialogOpen.value = false

    }
    else {
      const data = await response.json()
      snackbarText.value = data.error ?? "Something went wrong!"
    }

  } catch (error) {
    snackbarText.value = "Something went wrong!"
  }
  snackbarOpen.value = true
  setTimeout(() => {
    window.location.reload()
  }, 1500)
}
</script>

<template>
  <v-container>
    <SectionHeader title="Manage the Library" />

    <v-row>
      <v-col cols="12" md="8">
        <AddForm
            ref="addForm"
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
    <v-snackbar v-model="snackbarOpen" :timeout="3000">
      {{ snackbarText }}
    </v-snackbar>
  </v-container>
</template>