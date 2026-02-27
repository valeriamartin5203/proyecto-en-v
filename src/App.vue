<template>
  <div :class="tema">

    <!-- BOTÓN TEMA + SONIDO -->
    <div class="theme-switch p-2">
      <button class="btn btn-outline-light mb-2" @click="cambiarTema">
        Cambiar Tema
      </button>

      <div class="mt-2">
        <label class="text-white">🔔 Sonido de notificación:</label>

        <select v-model="sonidoSeleccionado" @change="cambiarSonido" class="form-select mt-1 mb-2">
          <option value="/sonidos/campanas.mp3">🔔 Campanas</option>
          <option value="/sonidos/c1.mp3">🎵 Sonido 2</option>
        </select>

        <button class="btn btn-warning btn-sm w-100" @click="probarSonido">
          ▶ Probar sonido
        </button>
      </div>
    </div>

    <div class="container-fluid">
      <div class="row">

        <!-- SIDEBAR -->
        <nav class="col-md-3 col-lg-2 p-3 vh-100">
          <h5>📚 Menú</h5>
          <button class="btn w-100 text-start mb-2" @click="currentView = 'organizar'">🗂 Organizar</button>
          <button class="btn w-100 text-start mb-2" @click="currentView = 'formulas'">📘 Fórmulas</button>
          <button class="btn w-100 text-start" @click="currentView = 'notas'">📝 Notas</button>
        </nav>

        <!-- CONTENIDO -->
        <main class="col-md-9 p-4">

          <!-- AGENDA -->
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

            <div class="mb-3">
              <button class="btn btn-outline-secondary me-2" @click="filtro='todas'">Todas</button>
              <button class="btn btn-outline-warning me-2" @click="filtro='pendientes'">Pendientes</button>
              <button class="btn btn-outline-success" @click="filtro='completadas'">Completadas</button>
            </div>

            <div v-for="(tarea, index) in tareasFiltradas" :key="index" class="card p-3 mb-3">
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

          <!-- FORMULAS -->
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

          <!-- NOTAS -->
          <div v-if="currentView === 'notas'">
            <h2>📝 Notas</h2>
            <input v-model="nuevoTituloNota" class="form-control mb-2" placeholder="Título de la nota" />
            <textarea v-model="nuevoContenidoNota" class="form-control mb-3" rows="4" placeholder="Escribe tu nota..."></textarea>
            <button class="btn btn-primary mb-4" @click="guardarNota">Guardar nota</button>

            <div v-for="(nota, index) in notas" :key="index" class="card p-3 mb-3">
              <h5>{{ nota.titulo }}</h5>
              <p>{{ nota.contenido }}</p>
              <button class="btn btn-danger btn-sm" @click="eliminarNota(index)">Eliminar</button>
            </div>
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
      temas: ["tema-oscuro", "tema-rosa", "tema-amarillo"],
      temaIndex: 0,
      tema: "tema-oscuro",

      currentView: "organizar",

      nuevaTarea: { titulo:"", descripcion:"", fecha:"", prioridad:"" },
      tareas: JSON.parse(localStorage.getItem("tareas")) || [],
      filtro: "todas",
      hoy: new Date().toISOString().split("T")[0],
      tareasNotificadas: [],

      nuevoTitulo: "",
      nuevaImagen: null,
      formulas: JSON.parse(localStorage.getItem("formulas")) || [],

      nuevoTituloNota: "",
      nuevoContenidoNota: "",
      notas: JSON.parse(localStorage.getItem("notas")) || [],

      sonidoSeleccionado: localStorage.getItem("sonido") || "/sonidos/campanas.mp3",
      audio: null
    }
  },

  computed: {
    tareasFiltradas() {
      if(this.filtro==='pendientes') return this.tareas.filter(t => !t.completada)
      if(this.filtro==='completadas') return this.tareas.filter(t => t.completada)
      return this.tareas
    }
  },

  mounted(){
    // Permitir sonido en navegadores modernos
    document.addEventListener("click", () => {
      if(this.audio){
        this.audio.play().then(()=>{this.audio.pause(); this.audio.currentTime=0}).catch(()=>{})
      }
    }, { once: true })

    const temaGuardado = localStorage.getItem("tema")
    if(temaGuardado){
      this.tema = temaGuardado
      this.temaIndex = this.temas.indexOf(temaGuardado)
    }

    if ("Notification" in window) {
      Notification.requestPermission()
    }

    this.cargarSonido()
    this.verificarTareas()
    setInterval(() => this.verificarTareas(), 60000)
  },

  methods: {
    cambiarTema() {
      this.temaIndex = (this.temaIndex + 1) % this.temas.length
      this.tema = this.temas[this.temaIndex]
      localStorage.setItem("tema", this.tema)
    },

    cargarSonido(){
      this.audio = new Audio(this.sonidoSeleccionado)
      this.audio.load()
    },

    cambiarSonido(){
      localStorage.setItem("sonido", this.sonidoSeleccionado)
      this.cargarSonido()
    },

    probarSonido(){
      if(this.audio){
        this.audio.currentTime=0
        this.audio.play().catch(err => console.log("Audio bloqueado:", err))
      }
    },

    verificarTareas(){
      this.tareas.forEach(tarea => {
        if(tarea.fecha===this.hoy && !tarea.completada && !this.tareasNotificadas.includes(tarea.titulo)){
          this.mostrarNotificacion(tarea.titulo)
          if(this.audio){
            this.audio.currentTime=0
            this.audio.play().catch(()=>{})
          }
          this.tareasNotificadas.push(tarea.titulo)
        }
      })
    },

    mostrarNotificacion(titulo){
      if(Notification.permission==="granted"){
        new Notification("📌 Tarea para hoy",{ body:`No olvides: ${titulo}` })
      }
    },

    agregarTarea(){
      if(!this.nuevaTarea.titulo) return
      this.tareas.push({...this.nuevaTarea, completada:false})
      localStorage.setItem("tareas", JSON.stringify(this.tareas))
      this.nuevaTarea = { titulo:"", descripcion:"", fecha:"", prioridad:"" }
    },

    toggleTarea(index){
      this.tareas[index].completada=!this.tareas[index].completada
      localStorage.setItem("tareas", JSON.stringify(this.tareas))
    },

    eliminarTarea(index){
      this.tareas.splice(index,1)
      localStorage.setItem("tareas", JSON.stringify(this.tareas))
    },

    subirImagen(event){
      const file = event.target.files[0]
      if(!file) return
      const reader = new FileReader()
      reader.onload=()=>this.nuevaImagen=reader.result
      reader.readAsDataURL(file)
    },

    guardarFormula(){
      if(!this.nuevoTitulo || !this.nuevaImagen) return
      this.formulas.push({titulo:this.nuevoTitulo, imagen:this.nuevaImagen})
      localStorage.setItem("formulas", JSON.stringify(this.formulas))
      this.nuevoTitulo=""
      this.nuevaImagen=null
    },

    eliminarFormula(index){
      this.formulas.splice(index,1)
      localStorage.setItem("formulas", JSON.stringify(this.formulas))
    },

    guardarNota(){
      if(!this.nuevoTituloNota || !this.nuevoContenidoNota) return
      this.notas.push({titulo:this.nuevoTituloNota, contenido:this.nuevoContenidoNota})
      localStorage.setItem("notas", JSON.stringify(this.notas))
      this.nuevoTituloNota=""
      this.nuevoContenidoNota=""
    },

    eliminarNota(index){
      this.notas.splice(index,1)
      localStorage.setItem("notas", JSON.stringify(this.notas))
    }
  }
}
</script>