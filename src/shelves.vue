<script setup>
import { ref } from "vue";
import SectionHeader from "./components/sectionHeader.vue"
import ShelfCard from "./components/shelfCard.vue"

const shelves = [
  {
    id: 1,
    name: 'Basement Shelf 1',
    location: 'Random place that is not lemons',
    image: 'Bookshelves-Empty.png',
    genres: ['Fantasy', 'Classic'],
    content: ['The Hobbit', 'Narnia'],
    description: 'Top shelf contains classic fantasy novels.'
  },
  {
    id: 2,
    name: 'Basement Shelf 2',
    location: 'Random place that is not lemons',
    image: 'Bookshelves-Empty.png',
    genres: ['Sci-Fi', 'Dystopian'],
    content: ['Dune', 'Foundation', '1984'],
    description: 'Science fiction and futuristic worlds.'
  },
  {
    id: 3,
    name: 'Basement Shelf 3',
    location: 'Random place that is not lemons',
    image: 'Bookshelves-Empty.png',
    genres: ['Philosophy', 'Psychology'],
    content: ['Meditations', 'Beyond Good and Evil'],
    description: 'Thought-provoking works and classics.'
  },
  {
    id: 4,
    name: 'Basement Shelf 4',
    location: 'Random place that is not lemons',
    image: 'Bookshelves-Empty.png',
    genres: ['History', 'Biography'],
    content: ['Sapiens', 'The Wright Brothers'],
    description: 'Historical accounts and biographies.'
  },
  {
    id: 5,
    name: 'Basement Shelf 5',
    location: 'Random place that is not lemons',
    image: 'Bookshelves-Empty.png',
    genres: ['Programming', 'Technology'],
    content: ['Clean Code', 'The Pragmatic Programmer'],
    description: 'Software development and tech books.'
  },
  {
    id: 6,
    name: 'Basement Shelf 6',
    location: 'Random place that is not lemons',
    image: 'Bookshelves-Empty.png',
    genres: ['Sci-Fi', 'Dystopian', 'Random Stuff','More','more','mooooore','colooors', 'ooooo','yes', 'iam having too much fun with this'],
    content: [],
    description: 'Spooky'
  }
]

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
        <ShelfCard :location="shelf.location" :description="shelf.description" :name="shelf.name" :image="shelf.image" :genres="shelf.genres" @view-details="openDetails(shelf)"  class="bg-black"></ShelfCard>
      </v-col>
    </v-row>

    <!--    Details Popup-->
    <v-dialog v-model="dialogOpen" max-width="650">
      <v-card v-if="selected" class="bg-black text-green-darken-3">
          <v-img :src="selected.image" height="220" cover/>
        <v-card-title class="text-h5">{{selected.name}}</v-card-title>
        <v-card-subtitle class="pb-0">{{selected.location}}</v-card-subtitle>
        <v-card-text class="pt-4">
          <div class="mb-3">
            <v-divider class="mb-4" />

            <h3 class="text-subtitle-1 mb-2">Books on this Shelf</h3>

            <v-chip
                v-for="(book, i) in selected.content"
                :key="book"
                :color="getBookColor(i)"
                class="mr-2 mb-2"
                variant="flat"
            >
              {{ book }}
            </v-chip>
          </div>
          <v-alert variant="tonal" type="info" class="mb-4" :text="`Details: ${selected.description}`"/>
          <v-divider class="mb-4"></v-divider>
        </v-card-text>
        <v-card-actions>
          <v-btn variant="tonal" @click="copyBookList(selected.content)">
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