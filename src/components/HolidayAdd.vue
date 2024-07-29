<script setup>
import { ref, computed } from 'vue'

const emit = defineEmits(['addHoliday'])

const startOfHoliday = ref(dateToString(new Date()))
const endOfHoliday = ref(startOfHoliday.value)
const newHoliday = ref('')

const props = defineProps({
  errorMessage: String,
  showForm: Boolean
})
defineOptions({
  inheritAttrs: false
})

const canAdd = computed(
  () =>
    (stringToDate(endOfHoliday.value) >= stringToDate(startOfHoliday.value))
)

const errorMessageToShow = computed(
  () => {
    if(!canAdd.value) return 'Start date must be after end date.' 
    return props.errorMessage
  }
    
)

function stringToDate(str) {
  const [y, m, d] = str.split('-')
  return new Date(+y, m - 1, +d)
}

function dateToString(date) {
  return (
    date.getFullYear() +
    '-' +
    pad(date.getMonth() + 1) +
    '-' +
    pad(date.getDate())
  )
}

function pad(n, s = String(n)) {
  return s.length < 2 ? `0${s}` : s
}

async function addHoliday() {
  emit('addHoliday', {name: newHoliday.value, startDate: startOfHoliday.value, endDate: endOfHoliday.value})
  newHoliday.value = ''
}

</script>

<template>
  <h1>Add holiday</h1>
  <form @submit.prevent="addHoliday" v-if="showForm">
    <label for="newHoliday" class="form-label">Label</label>
    <input v-model="newHoliday"  placeholder="Enjoying a summer holiday"><br>
    <label for="startOfHoliday" class="form-label">Start</label>
    <input v-model="startOfHoliday" type="date" :min="new Date().toISOString().split('T')[0]" value="2025-01-01"><br>
    <label for="endOfHoliday" class="form-label">End</label>
    <input v-model="endOfHoliday" type="date" :min="new Date().toISOString().split('T')[0]" value="2025-01-01"><br>

    <button :disabled="!canAdd">Save</button>
  </form>
  <div v-if="errorMessageToShow != ''" id="status" class="alert alert-primary" role="alert">
    {{errorMessageToShow}}
  </div>
</template>