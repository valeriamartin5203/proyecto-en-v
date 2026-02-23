<template>
  <div class="container">
    <h1>📚 Agenda de Tareas</h1>

    <form @submit.prevent="agregarTarea">
      <input 
        v-model="nuevaTarea.titulo" 
        placeholder="Nombre de la tarea" 
        required 
      />

      <input 
        v-model="nuevaTarea.materia" 
        placeholder="Materia" 
        required 
      />

      <input 
        type="date" 
        v-model="nuevaTarea.fecha" 
        required 
      />

      <button type="submit" class="btn-agregar">
        Agregar
      </button>
    </form>

    <div class="filtros">
      <button @click="filtro = 'todas'">Todas</button>
      <button @click="filtro = 'pendientes'">Pendientes</button>
      <button @click="filtro = 'completadas'">Completadas</button>
    </div>

    <ul>
      <li v-for="tarea in tareasFiltradas" :key="tarea.id">
        <div>
          <strong :class="{ completada: tarea.completada }">
            {{ tarea.titulo }}
          </strong>
          <p>
            {{ tarea.materia }} | {{ tarea.fecha }}
          </p>
        </div>

        <div>
          <button 
            @click="toggleTarea(tarea.id)" 
            class="btn-completar"
          >
            ✔
          </button>

          <button 
            @click="eliminarTarea(tarea.id)" 
            class="btn-eliminar"
          >
            ❌
          </button>
        </div>
      </li>
    </ul>

    <p>
      Tareas pendientes: 
      <strong>{{ pendientes }}</strong>
    </p>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const tareas = ref([])
const filtro = ref('todas')

const nuevaTarea = ref({
  titulo: '',
  materia: '',
  fecha: '',
  completada: false
})

const agregarTarea = () => {
  tareas.value.push({
    ...nuevaTarea.value,
    id: Date.now()
  })

  guardar()

  nuevaTarea.value = {
    titulo: '',
    materia: '',
    fecha: '',
    completada: false
  }
}

const eliminarTarea = (id) => {
  tareas.value = tareas.value.filter(t => t.id !== id)
  guardar()
}

const toggleTarea = (id) => {
  const tarea = tareas.value.find(t => t.id === id)
  tarea.completada = !tarea.completada
  guardar()
}

const tareasFiltradas = computed(() => {
  if (filtro.value === 'pendientes') {
    return tareas.value.filter(t => !t.completada)
  }
  if (filtro.value === 'completadas') {
    return tareas.value.filter(t => t.completada)
  }
  return tareas.value
})

const pendientes = computed(() => {
  return tareas.value.filter(t => !t.completada).length
})

const guardar = () => {
  localStorage.setItem('tareas', JSON.stringify(tareas.value))
}

onMounted(() => {
  const datos = localStorage.getItem('tareas')
  if (datos) {
    tareas.value = JSON.parse(datos)
  }
})
</script>