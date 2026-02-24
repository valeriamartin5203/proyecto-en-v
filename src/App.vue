<template>
  <div :class="tema">

    <!-- BOTÓN CAMBIAR TEMA -->
    <div class="theme-switch p-2">
      <button class="btn btn-outline-light" @click="cambiarTema">
        Cambiar Tema
      </button>
    </div>

    <div class="container-fluid">
      <div class="row">

        <!-- SIDEBAR -->
        <nav class="col-md-3 col-lg-2 p-3 vh-100">
          <h5>📚 Menú</h5>

          <button class="btn w-100 text-start mb-2" @click="currentView = 'organizar'">
            🗂 Organizar
          </button>

          <button class="btn w-100 text-start mb-2" @click="currentView = 'formulas'">
            📘 Fórmulas
          </button>

          <button class="btn w-100 text-start" @click="currentView = 'notas'">
            📝 Notas
          </button>
        </nav>

        <!-- CONTENIDO -->
        <main class="col-md-9 p-4">

          <!-- ================= AGENDA ================= -->
          <div v-if="currentView === 'organizar'">

            <h2>🗂 Agenda</h2>

            <input v-model="nuevaTarea.titulo" class="form-control mb-2" placeholder="Título" />
            <textarea v-model="nuevaTarea.descripcion" class="form-control mb-2" placeholder="Descripción"></textarea>
            <input type="date" v-model="nuevaTarea.fecha" class="form-control mb-2" />
            <select v-model="nuevaTarea.prioridad" class="form-control mb-2">
              <option disabled value="">Selecciona prioridad</option>
              <option>Alta</option>
              <option>Media</option>
              <option>Baja</option>
            </select>

            <button class="btn btn-primary mb-3" @click="agregarTarea">Agregar tarea</button>

            <!-- FILTROS -->
            <div class="mb-3">
              <button class="btn btn-outline-secondary me-2" @click="filtro='todas'">Todas</button>
              <button class="btn btn-outline-warning me-2" @click="filtro='pendientes'">Pendientes</button>
              <button class="btn btn-outline-success" @click="filtro='completadas'">Completadas</button>
            </div>

            <!-- LISTA -->
            <div v-for="(tarea, index) in tareasFiltradas" :key="index" class="card p-3 mb-3"
              :class="tarea.prioridad.toLowerCase()">
              <h5 :class="{ 'text-decoration-line-through': tarea.completada }">{{ tarea.titulo }}</h5>
              <p>{{ tarea.descripcion }}</p>
              <small>📅 {{ tarea.fecha }}</small><br>
              <small>⚡ Prioridad: {{ tarea.prioridad }}</small>

              <div class="mt-2">
                <button class="btn btn-success btn-sm me-2" @click="toggleTarea(index)">Completar</button>
                <button class="btn btn-danger btn-sm" @click="eliminarTarea(index)">Eliminar</button>
              </div>
            </div>

            <p class="fw-bold">Total tareas: {{ tareas.length }}</p>

          </div>

          <!-- ================= FORMULAS ================= -->
          <div v-if="currentView === 'formulas'">
            <h2>📘 Fórmulas</h2>

            <input v-model="nuevoTitulo" class="form-control mb-2" placeholder="Título de la fórmula" />
            <input type="file" @change="subirImagen" class="form-control mb-2" />
            <button class="btn btn-primary mb-4" @click="guardarFormula">Guardar fórmula</button>

            <div v-for="(formula, index) in formulas" :key="index" class="card p-3 mb-3">
              <h5>{{ formula.titulo }}</h5>
              <img :src="formula.imagen" style="max-width:100%" />
              <button class="btn btn-danger btn-sm mt-2" @click="eliminarFormula(index)">Eliminar</button>
            </div>
          </div>

          <!-- ================= NOTAS ================= -->
          <div v-if="currentView === 'notas'">
            <h2>📝 Notas</h2>
            <textarea v-model="nota" class="form-control" rows="6" placeholder="Escribe aquí..."></textarea>
          </div>

        </main>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // TEMAS
      temas: ["tema-oscuro", "tema-rosa", "tema-amarillo"],
      temaIndex: 0,
      tema: "tema-oscuro",

      // VISTAS
      currentView: "organizar",

      // AGENDA
      nuevaTarea: { titulo:"", descripcion:"", fecha:"", prioridad:"" },
      tareas: JSON.parse(localStorage.getItem("tareas")) || [],
      filtro: "todas",

      // FORMULAS
      nuevoTitulo: "",
      nuevaImagen: null,
      formulas: JSON.parse(localStorage.getItem("formulas")) || [],

      // NOTAS
      nota: localStorage.getItem("nota") || ""
    }
  },

  computed: {
    tareasFiltradas() {
      if(this.filtro==='pendientes') return this.tareas.filter(t => !t.completada)
      if(this.filtro==='completadas') return this.tareas.filter(t => t.completada)
      return this.tareas
    }
  },

  methods: {
    // ===== CAMBIAR TEMA =====
    cambiarTema() {
      this.temaIndex = (this.temaIndex + 1) % this.temas.length
      this.tema = this.temas[this.temaIndex]
    },

    // ===== AGENDA =====
    agregarTarea() {
      if(!this.nuevaTarea.titulo) return
      this.tareas.push({...this.nuevaTarea, completada:false})
      this.nuevaTarea = { titulo:"", descripcion:"", fecha:"", prioridad:"" }
    },
    toggleTarea(index) {
      this.tareas[index].completada = !this.tareas[index].completada
    },
    eliminarTarea(index) {
      this.tareas.splice(index,1)
    },

    // ===== FORMULAS =====
    subirImagen(event) {
      const file = event.target.files[0]
      if(!file) return
      const reader = new FileReader()
      reader.onload = () => { this.nuevaImagen = reader.result }
      reader.readAsDataURL(file)
    },
    guardarFormula() {
      if(!this.nuevoTitulo || !this.nuevaImagen) return
      this.formulas.push({titulo:this.nuevoTitulo, imagen:this.nuevaImagen})
      this.nuevoTitulo = ""
      this.nuevaImagen = null
    },
    eliminarFormula(index) {
      this.formulas.splice(index,1)
    }
  },

  watch: {

    // GUARDAR TAREAS AUTOMÁTICAMENTE
    tareas: {
      handler(valor) {
        localStorage.setItem("tareas", JSON.stringify(valor))
      },
      deep: true
    },

    // GUARDAR FORMULAS AUTOMÁTICAMENTE
    formulas: {
      handler(valor) {
        localStorage.setItem("formulas", JSON.stringify(valor))
      },
      deep: true
    },

    // GUARDAR NOTA AUTOMÁTICAMENTE
    nota(valor) {
      localStorage.setItem("nota", valor)
    }
  }
}
</script>