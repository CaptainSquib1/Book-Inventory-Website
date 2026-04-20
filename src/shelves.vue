<script setup>
import { ref, onMounted } from "vue"
import SectionHeader from "./components/sectionHeader.vue"
import ShelfCard from "./components/shelfCard.vue"

const shelves = ref([])

onMounted(async () => {
  const res = await fetch('http://localhost:3000/shelves')
  shelves.value = await res.json()
})

const bookColors = [
  'red',
  'orange',
  'yellow',
  'green',
  'blue',
  'purple'
]

function getBookColor(index) {
  return bookColors[index % bookColors.length]
}

const dialogOpen = ref(false)
const selected = ref(null)

const snackbarOpen= ref(false)
const snackbarText= ref('')

function openDetails(shelf) {
  selected.value = shelf
  dialogOpen.value = true
}

async function copyBookList(content) {
  try {
    await navigator.clipboard.writeText(content)
    snackbarText.value = 'Book List Copied!'

  } catch (e) {
    snackbarText.value = 'Could not auto-copy. Please copy manually.'
  }
  snackbarOpen.value = true
}
</script>

<template>
  <v-container>
    <SectionHeader
        title="Book Shelves"
        subtitle="Please click on details to view books.">

      <template #right>
        <v-chip variant="tonal" class="mr-2">Each day is a new day to read a book.</v-chip>
      </template>

    </SectionHeader>
    <v-row class="mt-2">
      <v-col v-for="shelf in shelves" :key="shelf.id" cols="12" sm="6" md="4">
        <ShelfCard
            :name="shelf.shelf_name"
            :description="`${shelf.books?.length ?? 0} book(s) on this shelf`"
            :genres="shelf.books?.map(b => b.genre).filter(Boolean)"
            @view-details="openDetails(shelf)"
            class="bg-black"
        />
      </v-col>
    </v-row>

    <!--    Details Popup-->
    <v-dialog v-model="dialogOpen" max-width="650">
      <v-card v-if="selected" class="bg-black text-green-darken-3">
        <v-card-title class="text-h5">{{selected.shelf_name}}</v-card-title>
        <v-card-subtitle class="pb-0">{{ selected.books?.length ?? 0 }} book(s)</v-card-subtitle>
        <v-card-text class="pt-4">
          <div class="mb-3">
            <v-divider class="mb-4" />

            <h3 class="text-subtitle-1 mb-2">Books on this Shelf</h3>

            <v-chip v-for="(book, i) in selected.books" :key="book.id" :color="getBookColor(i)" class="mr-2 mb-2" variant="flat">
              {{ book.name }}
            </v-chip>
          </div>
          <v-alert variant="tonal" type="info" class="mb-4" :text="`Authors: ${selected.books?.map(b => b.author).join(', ') || 'No books on this shelf'}`"/>        </v-card-text>
        <v-card-actions>
          <v-btn variant="tonal" @click="copyBookList(selected.books?.map(b => b.name).join(', '))">
            <v-icon start>mdi-content-copy</v-icon>
            Copy Book List
          </v-btn>
          <v-spacer/>
          <v-btn variant="flat" color="pink-darken-2" @click="dialogOpen = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-snackbar v-model="snackbarOpen" timeout="1800">
      {{snackbarText}}
      <template #actions>
        <v-btn variant="text" @click="snackbarOpen = false">Dismiss</v-btn>
      </template>
    </v-snackbar>
  </v-container>
</template>

<style scoped>

</style>