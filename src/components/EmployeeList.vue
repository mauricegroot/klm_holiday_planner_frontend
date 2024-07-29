<script setup>
import { ref } from 'vue'

const emit = defineEmits(['pickEmployee'])

const props = defineProps({
  employees: Array,
  pickedEmployee: Object
})

function pickEmployee(employee) {
  emit('pickEmployee', employee)
}
</script>

<template>
  <h1>Employees</h1>
  <ul v-if="employees.length > 0" class="list-group">
    <li v-for="employee in employees" :key="employee.id" class="list-group-item" :class="{active: employee==pickedEmployee}" @click="pickEmployee(employee)">
      <span>{{ employee.id }}</span> - <span>{{ employee.name }}</span>
    </li>
  </ul>
  <div v-else>
    No results
  </div>
</template>

<style>
a {
  text-decoration: none;
}
</style>