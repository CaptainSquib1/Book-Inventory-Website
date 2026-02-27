<script setup>
const shelves = defineProps({
  name: {type: String, required: true},
  location: {type: String, required: true},
  description: {type: String, required: true},
  content:{type:Array, default:() => ["Book o' Cheese", "Book o' Fries"]},
  image: {type: String, default:''},
  genres: {type: Array, default: () => []},
})

const emit = defineEmits(['view-details','open-shelf'])

const genreColors = [
    'red',
    'orange-darken-2',
    'yellow-darken-3',
    'green',
    'blue',
    'purple',
]

function getGenreColor(index) {
  return genreColors[index%genreColors.length];
}
</script>

<template>
  <v-card class="h-100">
    <v-img v-if="image" :src="image" height="170" cover/>
    <v-card-title class="text-h6">{{ name }}</v-card-title>
    <v-card-subtitle>{{ description }}</v-card-subtitle>
    <v-card-text>
      <div class="text-body-2 mb-3">
        <v-icon size="small" class="mr-1">mdi-map-marker</v-icon>
        {{ location }}
      </div>
      <div>
        <v-chip v-for="(genre, i) in genres" :key="genre" :color="getGenreColor(i)" class="mr-2 mb-2" size="small">{{ genre }}</v-chip>
      </div>
    </v-card-text>
    <v-card-actions>
      <v-btn variant="tonal" @click="emit('view-details')">
        <v-icon start>mdi-information-outline</v-icon>
        Details
      </v-btn>
    </v-card-actions>
  </v-card>
</template>

