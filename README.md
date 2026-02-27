## Project setup
# Agenda y Notas (Proyecto Libre)

Aplicación web pequeña creada con Vue 3 para gestionar tareas, guardar fórmulas (como imágenes) y tomar notas.

Características principales
- Agenda: crear tareas con título, descripción, fecha y prioridad; marcar completadas y filtrar.
- Fórmulas: subir y guardar imágenes con un título.
- Notas: crear, listar y eliminar notas simples.

Archivos importantes
- Interfaz principal: [src/App.vue](src/App.vue)
- Entrada de la app: [src/main.js](src/main.js)
- Estilos: [src/style.css](src/style.css)
- HTML base: [public/index.html](public/index.html)
- Configuración del proyecto: [package.json](package.json)

Instalación
1. Clona el repositorio o descarga el proyecto.
2. Instala dependencias:
# Agenda y Notas (Proyecto Libre)

Aplicación web pequeña creada con Vue 3 para gestionar tareas, guardar fórmulas (como imágenes) y tomar notas.

Características principales
- Agenda: crear tareas con título, descripción, fecha y prioridad; marcar completadas y filtrar.
- Fórmulas: subir y guardar imágenes con un título.
- Notas: crear, listar y eliminar notas simples.

Archivos importantes
- Interfaz principal: [src/App.vue](src/App.vue)
- Entrada de la app: [src/main.js](src/main.js)
- Estilos: [src/style.css](src/style.css)
- HTML base: [public/index.html](public/index.html)
- Configuración del proyecto: [package.json](package.json)

Instalación
1. Clona el repositorio o descarga el proyecto.
2. Instala dependencias:

```bash
npm install
```

Desarrollo (modo caliente)

```bash
npm run serve
```

Producción

```bash
npm run build
```

Notas de uso
- Los datos (tareas, fórmulas y notas) se almacenan en `localStorage` del navegador; no hay backend.
- Para agregar una fórmula usa el selector de archivos y guarda la imagen (se convierte a Data URL).
- Cambia entre temas con el botón "Cambiar Tema" en la esquina superior derecha.

Contribuir
- Pull requests bienvenidas. Abre un issue para discutir cambios mayores.

## Imagenes

<p align="center">
	<img src="public/imagenes/img1.PNG" width="700" alt="Captura de la app">
</p>

<p align="center">
	<img src="public/imagenes/img2.PNG" width="700" alt="Captura de la app">
</p>

<p align="center">
	<img src="public/imagenes/img3.PNG" width="700" alt="Captura de la app">
</p>

<p align="center">
	<img src="public/imagenes/img4.PNG" width="700" alt="Captura de la app">
</p>

<p align="center">
	<img src="public/imagenes/img5.PNG" width="700" alt="Captura de la app">
</p>

---
Si quieres, puedo también:
- Añadir instrucciones para desplegar en GitHub Pages o Netlify.
- Añadir tests o formato automático.
