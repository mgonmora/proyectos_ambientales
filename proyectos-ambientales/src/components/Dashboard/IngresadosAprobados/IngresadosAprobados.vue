<script setup>
import { ref, computed, onMounted } from 'vue'
import { Bar } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from 'chart.js'

// Registramos los componentes de Chart.js que se usarán
ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

// 🔹 Datos de ejemplo (puedes reemplazarlos con props o API)
const proyectos = ref([
  { fecha: '2021-04-10', estado: 'Aprobado' },
  { fecha: '2021-06-11', estado: 'Pendiente' },
  { fecha: '2022-02-12', estado: 'Aprobado' },
  { fecha: '2022-09-05', estado: 'Aprobado' },
  { fecha: '2023-03-14', estado: 'Pendiente' },
  { fecha: '2023-04-20', estado: 'Aprobado' },
])

// 🔹 Agrupar y contar por año
const resumenPorAño = computed(() => {
  const resumen = {}

  proyectos.value.forEach(p => {
    const año = new Date(p.fecha).getFullYear()

    if (!resumen[año]) {
      resumen[año] = { ingresados: 0, aprobados: 0 }
    }

    resumen[año].ingresados++
    if (p.estado === 'Aprobado') resumen[año].aprobados++
  })

  return resumen
})

// 🔹 Preparar datos para el gráfico
const chartData = computed(() => {
  const años = Object.keys(resumenPorAño.value).sort()
  return {
    labels: años,
    datasets: [
      {
        label: 'Ingresados',
        backgroundColor: '#42A5F5',
        data: años.map(año => resumenPorAño.value[año].ingresados),
      },
      {
        label: 'Aprobados',
        backgroundColor: '#66BB6A',
        data: años.map(año => resumenPorAño.value[año].aprobados),
      },
    ],
  }
})

const chartOptions = {
  responsive: true,
  plugins: {
    legend: {
      position: 'top',
    },
    title: {
      display: true,
      text: 'Proyectos Ingresados vs Aprobados por Año',
    },
  },
}
</script>

<template>
  <v-card class="pa-4">
    <v-card-title class="text-h6">Ingresados y Aprobados por Año</v-card-title>
    <v-card-text>
      <Bar :data="chartData" :options="chartOptions" />
    </v-card-text>
  </v-card>
</template>

<style scoped>
/* Estilos opcionales */
</style>
