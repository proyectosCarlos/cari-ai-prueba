<script setup>
import { ref, computed, onMounted } from 'vue';
import axios from 'axios';

const concepts = ref([{
  "id": "HO",
  "name": "HO",
  "start": "08:00",
  "end": "17:59"
},
{
  "id": "HED",
  "name": "HED",
  "start": "18:00",
  "end": "20:59"
},
{
  "id": "HEN",
  "name": "HEN",
  "start": "21:00",
  "end": "05:59"
}])

const conceptType = ref('HO')
const selectedConcept = ref({})
const endTime = ref('')
const startTime = ref('')

const initTime = ref('')
const finalTime = ref('')

const data = ref({
  "attendanceIn": initTime.value,
  "attendanceOut": finalTime.value,
  "concepts": concepts.value
})

const updateSelectedConcept = (id) => {
  const concept = concepts.value.find(c => c.id === id)
  selectedConcept.value = concept
  endTime.value = selectedConcept.value.end
  startTime.value = selectedConcept.value.start
}

const updateConcept = (id, newData) => {
  const concept = concepts.value.find(c => c.id === id)
  if (concept) {
    Object.assign(concept, newData)
  }
}

const calculateTime = () => {
  axios.post('https://falconcloud.co/site_srv10_ph/site/api/qserv.php/13465-770721', data.value)
    .then(response => {
      console.log(response.data)
    })
    .catch(error => {
      console.error(error)
    })
}



onMounted(() => {
  updateSelectedConcept(conceptType.value)
})
</script>


<template>

  <div class="h-screen w-screen flex justify-center items-center">
    <div class="bg-white w-10/12 h-11/12 shadow-md rounded-md flex flex-col md:flex-row text-gray-500">
      <div class=" w-full md:w-5/12 h-auto md:h-full flex flex-col items-center p-6 gap-3">
        <h1 class="text-2xl font-bold">Calcular tiempos App</h1>

        <form action="" class="flex flex-col gap-5 w-full items-center p-2  rounded-md shadow-sm">
          <label for="horario">Selecciona un concepto</label>
          <select class="select " v-model="conceptType"
            @change="updateSelectedConcept(conceptType, { start: '08:10', end: '09:10' })">
            <option v-for="concept in concepts" :key="concept.id" :value="concept.id">
              {{ concept.name }}
            </option>
          </select>

          <div class="w-full flex gap-5">
            <div class="flex flex-1 flex-col">
              <label for="startTime">Hora de inicio</label>
              <input type="time" class="input flex-1" v-model="startTime" name="appointment" />
            </div>

            <div class="flex flex-1 flex-col">
              <label for="startTime">Hora final</label>
              <input type="time" class="input flex-1" v-model="endTime" name="appointment" />
            </div>
          </div>
          <button class="btn btn-block btn-warning" type="button"
            @click="updateConcept(conceptType, { start: startTime, end: endTime })">Aplicar</button>
        </form>


        <ul class="list bg-base-100 rounded-box shadow-md w-full">
          <li class="p-4 pb-2 text-md underline opacity-60 tracking-wide">Esta es tu lista de conceptos</li>
          <li class="list-row items-center" v-for="concept in concepts" :key="concept.id">
            <span class="text-md font-bold">{{ concept.name }}:</span>
            <span class="text-green-700">Start: {{ concept.start }}</span>
            <span class="text-blue-500">End: {{ concept.end }}</span>
          </li>
        </ul>

        <div class="w-full flex gap-5">
          <div class="flex flex-1 flex-col">
            <label for="startTime">Hora de inicio</label>
            <input type="time" class="input flex-1" v-model="initTime" name="appointment" />
          </div>

          <div class="flex flex-1 flex-col">
            <label for="startTime">Hora final</label>
            <input type="time" class="input flex-1" v-model="finalTime" name="appointment" />
          </div>
        </div>

        <button class="btn btn-block btn-neutral" type="button" @click="calculateTime()"
          :disabled="initTime === '' || finalTime === ''">Consultar tiempo</button>

      </div>
      <div
        class="bg-yellow-400 w-full md:w-7/12 h-auto md:h-full shadow-sm rounded-l-2xl flex justify-center items-center">
        Graficos
      </div>
    </div>

  </div>

</template>
