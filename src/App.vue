<script setup>
import { ref, watchEffect } from 'vue'
import "bootstrap/dist/css/bootstrap.min.css"
import "bootstrap"
import EmployeeList from './components/EmployeeList.vue'
import HolidayList from './components/HolidayList.vue'
import HolidayAdd from './components/HolidayAdd.vue'

const API_URL = `http://localhost:8080`
const API_URL_HOLIDAYS = `${API_URL}/holidays`
const API_URL_EMPLOYEES = `${API_URL}/employees`

const holidays = ref([])
const employees = ref([])
const errorMessage = ref('')
const pickedEmployee = ref(null)
const showForm = ref(true)

watchEffect(async () => {
  errorMessage.value = '';
  holidays.value = await (await fetch(API_URL_HOLIDAYS)).json()
  employees.value = await (await fetch(API_URL_EMPLOYEES)).json()
  pickEmployee(null)
})

async function removeHoliday(holiday) {
  errorMessage.value = '';
  const response = await fetch(`${API_URL_HOLIDAYS}/${holiday.id}`, { method: 'DELETE' });
  if(response.ok) {
    holidays.value = holidays.value.filter((t) => t !== holiday)
  }else{
    handleErrorResponse(response)
  }
}

async function addHoliday(holiday) {
  errorMessage.value = '';
  var response = await fetch(`${API_URL_HOLIDAYS}`, { method: 'POST',
    headers: {'Content-Type': 'application/json'},
    body: JSON.stringify({ label: holiday.name, 
      employee: pickedEmployee.value.id, 
      startDate: holiday.startDate, 
      endDate: holiday.endDate})
    });
    if(response.ok) {
      var newHoliday = await response.json();
      holidays.value.push(newHoliday)
    }else{
      handleErrorResponse(response)
    }
} 

async function pickEmployee(employee) {
  pickedEmployee.value = pickedEmployee.value == employee ? null : employee;
  if(pickedEmployee.value == null) {
    errorMessage.value = 'Select employee'
    holidays.value = await (await fetch(API_URL_HOLIDAYS)).json()
  }else{
    errorMessage.value = '' 
    holidays.value = await (await fetch(API_URL_EMPLOYEES+'/'+employee.id+'/holidays')).json()
  }
  showForm.value = errorMessage.value == '';
}

async function handleErrorResponse(response) {
  const responseJson = await response.json();
  errorMessage.value = responseJson.message;
}
</script>

<template> 
  <header>
    <nav class="navbar">
      <div class="container-fluid">
        <span class="navbar-brand mb-0 h1">KLM Holiday Planner</span>
      </div>
    </nav>
  </header>
  <main>
    <div class="container">
      <div class="row">
        <div class="col">
          <EmployeeList :employees="employees" @pickEmployee="pickEmployee" :pickedEmployee="pickedEmployee" />
        </div>
        <div class="col">
          <HolidayList :holidays="holidays" @deleteHoliday="removeHoliday" />
        </div>
        <div class="col">
          <HolidayAdd  :errorMessage="errorMessage" @addHoliday="addHoliday" :showForm="showForm" />
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
.navbar {
  background-color: #009FD9;
  color: white;
}
.navbar-brand {
  color: white;
}
</style>
