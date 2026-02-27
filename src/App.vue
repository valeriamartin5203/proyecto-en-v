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
      if(this.filtro==='pendientes')
        return this.tareas.filter(t => !t.completada)

      if(this.filtro==='completadas')
        return this.tareas.filter(t => t.completada)

      return this.tareas
    }
  },

  mounted(){

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

    setInterval(() => {
      this.verificarTareas()
    }, 60000)
  },

  methods: {

    // ================= TEMA =================
    cambiarTema() {
      this.temaIndex = (this.temaIndex + 1) % this.temas.length
      this.tema = this.temas[this.temaIndex]
      localStorage.setItem("tema", this.tema)
    },

    // ================= SONIDO =================
    cargarSonido(){
      this.audio = new Audio(this.sonidoSeleccionado)
    },

    cambiarSonido(){
      localStorage.setItem("sonido", this.sonidoSeleccionado)
      this.cargarSonido()
    },

    probarSonido(){
      if(this.audio){
        this.audio.currentTime = 0
        this.audio.play()
      }
    },

    // ================= NOTIFICACIONES =================
    verificarTareas(){

      const hoyActual = new Date().toISOString().split("T")[0]

      this.tareas.forEach((tarea, index) => {

        // Si la tarea no tiene fechaNotificada, se la agregamos (compatibilidad con tareas antiguas)
        if(tarea.fechaNotificada === undefined){
          this.tareas[index].fechaNotificada = null
        }

        if(
          tarea.fecha === hoyActual &&
          !tarea.completada &&
          tarea.fechaNotificada !== hoyActual
        ){

          this.mostrarNotificacion(tarea.titulo)

          if(this.audio){
            this.audio.currentTime = 0
            this.audio.play()
          }

          this.tareas[index].fechaNotificada = hoyActual
          localStorage.setItem("tareas", JSON.stringify(this.tareas))
        }

      })
    },

    mostrarNotificacion(titulo){
      if(Notification.permission === "granted"){
        new Notification("📌 Tarea para hoy", {
          body: `No olvides: ${titulo}`
        })
      }
    },

    // ================= AGENDA =================
    agregarTarea() {

      if(!this.nuevaTarea.titulo) return

      this.tareas.push({
        ...this.nuevaTarea,
        completada:false,
        fechaNotificada:null
      })

      localStorage.setItem("tareas", JSON.stringify(this.tareas))

      this.nuevaTarea = {
        titulo:"",
        descripcion:"",
        fecha:"",
        prioridad:""
      }
    },

    toggleTarea(index) {
      this.tareas[index].completada =
        !this.tareas[index].completada

      localStorage.setItem("tareas", JSON.stringify(this.tareas))
    },

    eliminarTarea(index) {
      this.tareas.splice(index,1)
      localStorage.setItem("tareas", JSON.stringify(this.tareas))
    },

    // ================= FORMULAS =================
    subirImagen(event) {
      const file = event.target.files[0]
      if(!file) return

      const reader = new FileReader()
      reader.onload = () => {
        this.nuevaImagen = reader.result
      }
      reader.readAsDataURL(file)
    },

    guardarFormula() {
      if(!this.nuevoTitulo || !this.nuevaImagen) return

      this.formulas.push({
        titulo:this.nuevoTitulo,
        imagen:this.nuevaImagen
      })

      localStorage.setItem("formulas", JSON.stringify(this.formulas))

      this.nuevoTitulo = ""
      this.nuevaImagen = null
    },

    eliminarFormula(index) {
      this.formulas.splice(index,1)
      localStorage.setItem("formulas", JSON.stringify(this.formulas))
    },

    // ================= NOTAS =================
    guardarNota() {
      if(!this.nuevoTituloNota ||
         !this.nuevoContenidoNota) return

      this.notas.push({
        titulo: this.nuevoTituloNota,
        contenido: this.nuevoContenidoNota
      })

      localStorage.setItem("notas", JSON.stringify(this.notas))

      this.nuevoTituloNota = ""
      this.nuevoContenidoNota = ""
    },

    eliminarNota(index) {
      this.notas.splice(index,1)
      localStorage.setItem("notas", JSON.stringify(this.notas))
    }

  }
}
</script>