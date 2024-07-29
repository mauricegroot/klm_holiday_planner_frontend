<script setup>
import {differenceInDays} from 'date-fns/differenceInDays';

const MIN_DAYS_IN_ADVANCE_DELETE = 5;

const props = defineProps({
  holidays: Array
})

const emit = defineEmits(['deleteHoliday'])

function deleteHoliday(holiday) {
    emit('deleteHoliday', holiday)
}
</script>

<template>
  <h1>Holidays</h1>
  <ul v-if="holidays.length > 0" class="list-group">
    <li v-for="holiday in holidays" :key="holiday.id" class="list-group-item">
      <span class="label">{{ holiday.label }}</span><br>
      Employee: <span class="employee">{{ holiday.employee.name }}</span><br>
      Date: <span v-if="holiday.startDate == holiday.endDate">on <span class="date">{{ (holiday.startDate) }}</span><br></span>
      <span v-else>from <span class="date">{{ (holiday.startDate) }}</span> till <span class="date">{{ (holiday.endDate) }}</span><br></span>
      <a href="#" v-if="differenceInDays(new Date(holiday.endDate), new Date()) > MIN_DAYS_IN_ADVANCE_DELETE" @click="deleteHoliday(holiday)">Cancel</a>
      <span v-else style="text-decoration: line-through" @click="deleteHoliday(holiday)">Cancel</span>
    </li>
  </ul>
  <div v-else>
    No results
  </div>
</template>

<style>
.employee {
  font-style: italic;
}
.label,
.date {
  font-weight: bold;
}
</style>