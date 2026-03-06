<script setup>
import { ref } from "vue"

const props = defineProps({
  order: Object,
  menu: Array,
  locations: Array,
})

const emit = defineEmits(["update:order","submit"])

const step = ref(1)
const formUser = ref(null)
const formBook = ref(null)

function setField(key,value){
  emit("update:order",{...props.order,[key]:value})
}

async function nextval(){
  const r = await formUser.value.validate()
  if(r.valid) step.value=2
}
function next() {
  step.value += 1
}
function back(){
  step.value -= 1
}
</script>

<template>
  <v-stepper v-model="step">
    <v-stepper-header>
      <v-stepper-item :value="1" title="User Name"/>
      <v-stepper-item :value="2" title="Function"/>
      <v-stepper-item :value="3" title="Books"/>
    </v-stepper-header>

    <v-stepper-window>
      <v-stepper-window-item :value="1">
        <v-form ref="formUser">
          <v-text-field
              :model-value="order.userName"
              @update:model-value="v=>setField('userName',v)"
              label="Name"
          />
          <v-btn :disabled="!order.userName" @click="nextval">Next</v-btn>
        </v-form>
      </v-stepper-window-item>

      <v-stepper-window-item :value="2">
        <v-select
            :items="menu"
            item-title="name"
            item-value="id"
            :model-value="order.items"
            @update:model-value="v=>setField('items',v)"
        />

        <v-btn :disabled="!order.items" @click="next">Next</v-btn>
        <v-btn @click="back">Back</v-btn>
      </v-stepper-window-item>

      <v-stepper-window-item :value="3">
        <v-form ref="formBook">
          <v-text-field
              v=""
              :model-value="order.books"
              @update:model-value="v=>setField('books',v)"
              label="Books"/>


          <v-btn @click="back">Back</v-btn>
          <v-btn :disabled="!order.books" @click="$emit('submit')">
            Review & Submit
          </v-btn>
        </v-form>
      </v-stepper-window-item>

    </v-stepper-window>
  </v-stepper>
</template>