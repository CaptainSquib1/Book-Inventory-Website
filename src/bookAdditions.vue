<script setup>
import { ref, computed } from "vue"
import SectionHeader from "./components/sectionHeader.vue"
import AddForm from "./components/orderForm.vue"
import AddSummary from "./components/orderSummary.vue"
import ConfirmDialog from "./components/confirmDialog.vue"

const menu = [
  { id: 1, name: "Add Book"},
  { id: 2, name: "Remove Book"},
]

const locations = ["Downstairs", "1st Floor", "2nd Floor"]

const order = ref({
  userName: "",
  email: "",
  location: null,
  books: ["yay","woo","cheese"],
  author: "change to dictionary",   // TODO: change books and author to a dictionary
  agree: false,
  pdf:"", // TODO: implement dropzone for
})

const dialogOpen = ref(false)
const snackbarOpen = ref(false)
const snackbarText = ref("")

function openConfirm() {
  dialogOpen.value = true
}

function confirmAddition() {
  dialogOpen.value = false
  snackbarText.value = "Book Added!"
  snackbarOpen.value = true
}
</script>

<template>
  <v-container>
    <SectionHeader title="Add a New Book" />

    <v-row>
      <v-col cols="12" md="8">
        <AddForm
            v-model:order="order"
            :menu="menu"
            :locations="locations"
            @submit="openConfirm"
        />
      </v-col>

      <v-col cols="12" md="4">
        <AddSummary :order="order" :menu="menu" />
      </v-col>
    </v-row>

    <ConfirmDialog
        v-model:open="dialogOpen"
        :order="order"
        :menu="menu"
        @confirm="confirmAddition"
    />
  </v-container>
</template>