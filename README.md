# Proyecto de Visualización de Datos Ambientales 🌎📊

Este proyecto es una aplicación desarrollada con **Vue 3**, **Vuetify 3** y **Chart.js**, destinada a visualizar indicadores relacionados con proyectos ambientales, tales como tiempo de tramitación, estado de aprobación y distribución geográfica por región y tipo de proyecto.

# Requisitos Previos
Antes de ejecutar este proyecto, tener instalado lo siguiente:

Node.js (recomendado: versión 18.x o superior)
npm (viene junto con Node.js)

# Instrucciones de instalación

## 1. Instalar dependencias
npm install

## 2. Ejecutar en modo desarrollo
npm run dev

# 🧩 Tecnologías usadas

- **Vue 3**: Framework progresivo para la construcción de interfaces.
- **Vuetify 3**: Framework de componentes UI basado en Material Design.
- **Chart.js** + **vue-chartjs**: Librería para la creación de gráficos interactivos.
- **Vite**: Herramienta de desarrollo rápida para Vue.
- **JavaScript moderno** con `<script setup>` y composición reactiva.

# 📊 Funcionalidades implementadas

- Visualización de gráficos de barra (horizontal y vertical).
- Filtros por región con selección de "Todas las regiones" por defecto.
- Agrupación de datos por año y tipo de proyecto.
- Cálculo automático del tiempo de tramitación de los proyectos (días entre `fecha` y `fecha_resolucion`).
- Gráficos adaptables (responsive) que se ajustan a pantalla de escritorio, tablet y móvil.
- Imagen de fondo con opacidad en los gráficos.
- Tooltip personalizado para los valores del gráfico.
- Oculta datos con fechas inválidas o tiempos de tramitación <= 0.
- Media query para ocultar fondos en pantallas móviles.

# 🎨 Diseño y UI

- Basado en la paleta **Teal** (`bg-teal-darken-3`) de Vuetify similar a los colores corporativos de DSS.
- Se usaron colores legibles y que tengan buen contraste

