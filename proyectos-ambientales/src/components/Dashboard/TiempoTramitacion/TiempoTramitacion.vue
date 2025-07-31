<script setup>
import { computed } from "vue";
import { Bar } from "vue-chartjs";
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from "chart.js";

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale);

const props = defineProps({
  proyectos: {
    type: Array,
    default: () => [],
  },
});


function parseDMY(fechaStr) {
  if (!fechaStr) return null;
  const partes = fechaStr.split("-");
  if (partes.length !== 3) return null;
  const dia = partes[0];
  const mes = partes[1];
  const año = partes[2];
  const fechaIso = `${año}-${mes}-${dia}`;
  const fecha = new Date(fechaIso);
  return isNaN(fecha) ? null : fecha;
}

function calcularDiasEntre(fechaInicio, fechaFin) {
  if (!fechaInicio || !fechaFin) return null;
  const diffMs = fechaFin.getTime() - fechaInicio.getTime();
  return Math.round(diffMs / (1000 * 60 * 60 * 24));
}
const resumenPorAñoTipo = computed(() => {
  const agrupado = {};

  props.proyectos.forEach((p) => {
    const fechaInicio = parseDMY(p.fecha);
    const fechaResolucion = parseDMY(p.fecha_resolucion);
    let diasTramite = fechaInicio && fechaResolucion ? calcularDiasEntre(fechaInicio, fechaResolucion) : null;

    if (diasTramite === null || diasTramite <= 0) return;

    const año = fechaInicio.getFullYear();
    const tipo = p.tipo || "Otro";

    if (!agrupado[año]) agrupado[año] = {};
    if (!agrupado[año][tipo]) agrupado[año][tipo] = 0;

    agrupado[año][tipo] += diasTramite;
  });

  return agrupado;
});

const años = computed(() => Object.keys(resumenPorAñoTipo.value).sort((a, b) => a - b));

const tiposUnicos = computed(() => {
  const tipos = new Set();
  Object.values(resumenPorAñoTipo.value).forEach((tiposPorAño) => {
    Object.keys(tiposPorAño).forEach((tipo) => tipos.add(tipo));
  });
  return Array.from(tipos).sort();
});

const chartData = computed(() => {
  const labels = años.value;
  const datasets = tiposUnicos.value.map((tipo, idx) => {
    return {
      label: tipo,
      data: labels.map((año) => resumenPorAñoTipo.value[año][tipo] || 0),
      backgroundColor: ["#4DB6AC", "#3F51B5", "#FFA726", "#AB47BC", "#26A69A"][idx % 5],
      borderColor: "transparent",
      borderWidth: 1,
    };
  });

  return { labels, datasets };
});

const chartOptions = {
  maintainAspectRatio: false,
  responsive: true,
  indexAxis: "x",
  plugins: {
    legend: {
      position: "top",
      labels: {
        color: "#333",
        font: { weight: "bold" },
      },
    },
    title: {
      display: true,
      text: "Días de Tramitación agrupados por Año y Tipo",
      color: "#222",
      font: { size: 18, weight: "bold" },
      padding: { top: 10, bottom: 25 },
    },
    tooltip: {
      callbacks: {
        label(ctx) {
          return `${ctx.dataset.label}: ${ctx.parsed.y.toLocaleString()} días`;
        },
      },
    },
  },
  scales: {
    y: {
      beginAtZero: true,
      title: {
        display: true,
        text: "Días acumulados",
        color: "#333",
        font: { weight: "bold", size: 14 },
      },
      ticks: { color: "#333" },
      grid: { color: "#eee" },
    },
    x: {
      title: {
        display: true,
        text: "Año",
        color: "#333",
        font: { weight: "bold", size: 14 },
      },
      ticks: { color: "#333" },
      grid: { display: true },
    },
  },
};
</script>

<template>
  <v-card class="pa-4 responsive-card">
    <v-card-text>
      <div class="chart-background">
        <Bar :data="chartData" :options="chartOptions" />
      </div>
    </v-card-text>
  </v-card>

  
</template>
