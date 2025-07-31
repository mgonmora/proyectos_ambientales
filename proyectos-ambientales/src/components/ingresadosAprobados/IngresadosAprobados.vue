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

function formatDMY(date) {
  if (!date) return "";
  const day = date.getDate();
  const month = date.getMonth() + 1;
  const year = date.getFullYear();
  const dd = day < 10 ? "0" + day : "" + day;
  const mm = month < 10 ? "0" + month : "" + month;
  return `${dd}-${mm}-${year}`;
}

const proyectosProcesados = computed(() => {
  return props.proyectos.map((p) => ({
    ...p,
    dateObj: parseDMY(p.fecha),
  }));
});

const resumenPorAño = computed(() => {
  const agrupado = {};
  proyectosProcesados.value.forEach((p) => {
    if (p.dateObj) {
      const año = p.dateObj.getFullYear();
      if (!agrupado[año]) {
        agrupado[año] = { ingresados: 0, aprobados: 0, ejemplo: p.dateObj };
      }
      agrupado[año].ingresados++;
      if (p.estado === "Aprobado") {
        agrupado[año].aprobados++;
      }
    }
  });
  return agrupado;
});

const chartData = computed(() => {
  const años = Object.keys(resumenPorAño.value);
  años.sort((a, b) => Number(a) - Number(b));
  const ingresadosData = [];
  const aprobadosData = [];
  for (let i = 0; i < años.length; i++) {
    const año = años[i];
    ingresadosData.push(resumenPorAño.value[año].ingresados);
    aprobadosData.push(resumenPorAño.value[año].aprobados);
  }
  return {
    labels: años,
    datasets: [
      {
        label: "Ingresados",
        backgroundColor: "#FF8A65",
        borderColor: "#FF7043",
        borderWidth: 1.5,
        data: ingresadosData,
      },
      {
        label: "Aprobados",
        backgroundColor: "#4DB6AC",
        borderColor: "#009688",
        borderWidth: 1.5,
        data: aprobadosData,
      },
    ],
  };
});

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  scales: {
      x: {
        title: {
          display: true,
          text: "Año",
          color: "#333",
          font: { weight: "bold", size: 14 },
        },
      },
    },
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
      text: "Proyectos Ingresados vs Aprobados por Año",
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
        title: function (tooltipItems) {
          const dato = tooltipItems[0];
          const año = dato.label;
          const fechaEjemplo = resumenPorAño.value[año].ejemplo;
          const textoFecha = fechaEjemplo ? formatDMY(fechaEjemplo) : "";
          return "Fecha: " + textoFecha;
        },
        label: function (tooltipItem) {
          const año = tooltipItem.label;
          const dataAño = resumenPorAño.value[año];
          return [
            "Ingresados: " + dataAño.ingresados,
            "Aprobados: " + dataAño.aprobados,
          ];
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
