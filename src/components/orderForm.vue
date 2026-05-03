<script setup>
import { ref, computed, onMounted } from "vue"

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

function setProcess(value) {
  emit("update:order", {
    ...props.order,
    process: value,
    shelfId: null,
    bookId: null,
    bookName: "",
    bookAuthor: "",
    bookDescription: "",
    bookGenre: "",
    bookImage: "",
  })
}


function next() { step.value += 1 }
function back() { step.value -= 1 }


onMounted(async () => {
  const [shelvesRes, booksRes] = await Promise.all([
    fetch('/api/shelves'),
    fetch('/api/books'),
  ])
  shelves.value = await shelvesRes.json()
  books.value = await booksRes.json()
})


// Books on the selected shelf
const shelfBooks = computed(() => {
  if (!props.order.shelfId) return []
  const shelf = shelves.value.find(s => s.id === props.order.shelfId)
  if (!shelf) {return []}
  if (!shelf.books) {return []}
  return shelf.books
})

const shelfBookIds = computed(() => shelfBooks.value.map(b => b.id))

// placeBook: exclude books already on this shelf
const availableBooks = computed(() => {
  if (props.order.process !== "placeBook") return books.value
  return books.value.filter(b => !shelfBookIds.value.includes(b.id))
})

// unplaceBook: only books on this shelf
const removableBooks = computed(() => shelfBooks.value)

// Step 3 is valid when required fields are filled
const step3Valid = computed(() => {
  if (props.order.process === "addBook") return props.order.bookName && props.order.bookAuthor
  return props.order.bookId
})

const processOptions = [
  { title: "Place a book on a shelf", value: "placeBook" },
  { title: "Remove a book from a shelf", value: "unplaceBook" },
  { title: "Add a new book to the library", value: "addBook" },
]

// get genres from books data
const genres = computed(() => [
  ...new Set(books.value.map(b => b.genre).filter(Boolean))
])



</script>

<template>
  <v-stepper v-model="step" bg-color="green-lighten-3">
    <v-stepper-header>
      <v-stepper-item :value="1" title="Your Name" color="orange-lighten-2"/>
      <v-stepper-item :value="2" title="Action" color="orange-lighten-2"/>
      <v-stepper-item :value="3" :title="order.process === 'addBook' ? 'Book Details' : 'Select Book'" color="orange-lighten-2"/>
      <v-stepper-item v-if="order.process === 'addBook'" :value="4" title="More Details" color="orange-lighten-2"/>
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
              bg-color="orange-lighten-3"
          />
          <v-btn :disabled="!order.userName" @click="next" color="orange-lighten-3">Next</v-btn>
        </v-form>
      </v-stepper-window-item>

      <!-- Step 2: Pick a process -->
      <v-stepper-window-item :value="2">
        <v-select
            :items="processOptions"
            item-title="title"
            item-value="value"
            :model-value="order.process"
            @update:model-value="setProcess"
            label="What would you like to do?"
            bg-color="orange-lighten-3"
        />
        <v-btn @click="back" color="red-lighten-3">Back</v-btn>
        <v-btn :disabled="!order.process" @click="next" color="orange-lighten-3">Next</v-btn>
      </v-stepper-window-item>

      <!-- Step 3a: placeBook / unplaceBook pick shelf then book -->
      <v-stepper-window-item
          v-if="order.process === 'placeBook' || order.process === 'unplaceBook'"
          :value="3"
          bg-color="orange-lighten-3"
      >
        <v-select
            :items="shelves"
            item-title="shelf_name"
            item-value="id"
            :model-value="order.shelfId"
            @update:model-value="v => setField('shelfId', v)"
            label="Select a Shelf"
            bg-color="orange-lighten-3"
        />

        <!-- placeBook: books not on this shelf -->
        <v-select
            v-if="order.process === 'placeBook'"
            :items="availableBooks"
            item-title="name"
            item-value="id"
            :model-value="order.bookId"
            @update:model-value="v => setField('bookId', v)"
            label="Select a Book to Place"
            :disabled="!order.shelfId"
            :no-data-text="order.shelfId ? 'All books are already on this shelf' : 'Select a shelf first'"
            bg-color="orange-lighten-3"
        />

        <!-- unplaceBook: books on this shelf -->
        <v-select
            v-if="order.process === 'unplaceBook'"
            :items="removableBooks"
            item-title="name"
            item-value="id"
            :model-value="order.bookId"
            @update:model-value="v => setField('bookId', v)"
            label="Select a Book to Remove"
            :disabled="!order.shelfId"
            :no-data-text="order.shelfId ? 'No books on this shelf' : 'Select a shelf first'"
            bg-color="orange-lighten-3"
        />

        <v-btn @click="back" color="red-lighten-3">Back</v-btn>
        <v-btn :disabled="!step3Valid" @click="$emit('submit')" color="orange-lighten-3">
          Review & Submit
        </v-btn>
      </v-stepper-window-item>

      <!-- Step 3b: addBook required-->
      <v-stepper-window-item v-if="order.process === 'addBook'" :value="3">
        <v-text-field
            :model-value="order.bookName"
            @update:model-value="v => setField('bookName', v)"
            label="Book Title"
            :rules="[v => !!v || 'Title is required']"
            bg-color="orange-lighten-3"
        />
        <v-text-field
            :model-value="order.bookAuthor"
            @update:model-value="v => setField('bookAuthor', v)"
            label="Author"
            :rules="[v => !!v || 'Author is required']"
            bg-color="orange-lighten-3"
        />
        <v-btn @click="back" color="red-lighten-3">Back</v-btn>
        <v-btn :disabled="!step3Valid" @click="next" color="orange-lighten-3">Next</v-btn>
      </v-stepper-window-item>

      <!-- Step 4: addBook optional fields -->
      <v-stepper-window-item v-if="order.process === 'addBook'" :value="4">
        <v-text-field
            :model-value="order.bookDescription"
            @update:model-value="v => setField('bookDescription', v)"
            label="Description (optional)"
            bg-color="orange-lighten-3"
        />
        <v-combobox
            :model-value="order.bookGenre"
            @update:model-value="v => setField('bookGenre', v)"
            label="Genre (optional)"
            :items="genres"
            bg-color="orange-lighten-3"
        />
        <v-text-field
            :model-value="order.bookImage"
            @update:model-value="v => setField('bookImage', v)"
            label="Image file name (optional)"
            bg-color="orange-lighten-3"
        />
        <v-btn @click="back" color="red-lighten-3">Back</v-btn>
        <v-btn @click="$emit('submit')" color="orange-lighten-3">Review & Submit</v-btn>
      </v-stepper-window-item>

    </v-stepper-window>
  </v-stepper>
</template>