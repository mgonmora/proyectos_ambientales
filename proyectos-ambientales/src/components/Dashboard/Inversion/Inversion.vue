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

function parseInversion(str) {
  if (!str) return 0;
  const limpio = str.replace(/\./g, "").replace(",", ".");
  const num = parseFloat(limpio);
  return isNaN(num) ? 0 : num;
}

const proyectosProcesados = computed(() =>
  props.proyectos.map((p) => ({
    ...p,
    dateObj: parseDMY(p.fecha),
    inversionNum: parseInversion(p.inversion),
  }))
);

const resumenPorAñoTipo = computed(() => {
  const agrupado = {};
  proyectosProcesados.value.forEach((p) => {
    if (p.dateObj) {
      const año = p.dateObj.getFullYear();
      if (!agrupado[año]) {
        agrupado[año] = { DIA: 0, EIA: 0 };
      }
      const tipo = p.tipo === "DIA" ? "DIA" : p.tipo === "EIA" ? "EIA" : null;
      if (tipo) {
        agrupado[año][tipo] += p.inversionNum;
      }
    }
  });
  return agrupado;
});

const chartData = computed(() => {
  const años = Object.keys(resumenPorAñoTipo.value).sort((a, b) => Number(a) - Number(b));
  const diaData = [];
  const eiaData = [];
  for (const año of años) {
    diaData.push(resumenPorAñoTipo.value[año].DIA || 0);
    eiaData.push(resumenPorAñoTipo.value[año].EIA || 0);
  }
  return {
    labels: años,
    datasets: [
      {
        label: "Inversión DIA",
        backgroundColor: "#29B6F6",
        borderColor: "#CDDC39",
        borderWidth: 1,
        data: diaData,
      },
      {
        label: "Inversión EIA",
        backgroundColor: "#66BB6A",
        borderColor: "#388E3C",
        borderWidth: 1,
        data: eiaData,
      },
    ],
  };
});

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  interaction: {
    mode: "index",
    intersect: false,
  },
  scales: {
    x: {
      stacked: true,
      title: {
        display: true,
        text: "Año",
        color: "#333",
        font: { weight: "bold", size: 14 },
      },
      ticks: { color: "#333" },
      grid: { display: true },
    },
    y: {
      stacked: true,
      title: {
        display: true,
        text: "Inversión (unidades monetarias)",
        color: "#333",
        font: { weight: "bold", size: 14 },
      },
      ticks: { color: "#333", beginAtZero: true },
      grid: { color: "#eee" },
    },
  },
  plugins: {
    legend: {
      position: "top",
      labels: { color: "#333", font: { weight: "bold" } },
    },
    title: {
      display: true,
      text: "Inversión por Año y Tipo de Proyecto (DIA/EIA)",
      color: "#222",
      font: { size: 18, weight: "bold" },
      padding: { top: 10, bottom: 25 },
    },
    tooltip: {
      backgroundColor: "#2c3e50",
      titleColor: "#ecf0f1",
      bodyColor: "#ecf0f1",
      borderColor: "#34495e",
      borderWidth: 1,
      padding: 10,
      cornerRadius: 5,
      callbacks: {
        label: function (tooltipItem) {
          return (
            tooltipItem.dataset.label +
            ": " +
            tooltipItem.parsed.y.toLocaleString(undefined, {
              minimumFractionDigits: 2,
              maximumFractionDigits: 2,
            })
          );
        },
      },
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
