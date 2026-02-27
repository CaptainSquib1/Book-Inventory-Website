<script setup>
defineProps({
  title: { type: String, required: true },
  subtitle: { type:String, required: false, default: '' },

})
</script>

<template>
  <div class="d-flex align-start flex-wrap ga-3 justify-space-between">
    <div>
      <h1 class="text-h4 mb-1">{{ title }}</h1>
      <div v-if="subtitle" class="text-body-1 text-medium-emphasis">
        {{ subtitle }}
      </div>
    </div>

    <div class="d-flex align-center flex-wrap ga-2">
      <slot name="right"></slot>
    </div>
  </div>
  <v-divider class="mt-4"></v-divider>

</template>

