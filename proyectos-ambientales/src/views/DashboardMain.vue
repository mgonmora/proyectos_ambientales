<script setup>
import IngresadosAprobados from '../components/ingresadosAprobados/IngresadosAprobados.vue'
import Inversion from '../components/inversion/Inversion.vue'
import TiempoTramitacion from '../components/tiempoTramitacion/TiempoTramitacion.vue'
import RegionFilter from '../components/regionFilter/RegionFilter.vue';
import { ref, onMounted, computed } from "vue";

const proyectos = ref([]);
const regionSeleccionada = ref("");

// Carga del archivo json
onMounted(async () => {
  const mod = await import("../data/proyectos.json");
  proyectos.value = mod.default;
});

const proyectosFiltrados = computed(() => {
  console.log(proyectos.value)
  if (!regionSeleccionada.value || regionSeleccionada.value === "") {
    return proyectos.value;
  }
  return proyectos.value.filter(
    (p) => p.region && p.region === regionSeleccionada.value
  );
});
</script>

<template>
  <v-container class="py-6">
    <v-row dense>
      <RegionFilter v-model="regionSeleccionada" :proyectos="proyectos" />
    </v-row>

    <v-row dense>
      <v-col cols="12" md="6">
        <IngresadosAprobados :proyectos="proyectosFiltrados" />
      </v-col>
      <v-col cols="12" md="6">
        <Inversion :proyectos="proyectosFiltrados" />
      </v-col>
      <v-col cols="12" md="12">
        <TiempoTramitacion :proyectos="proyectosFiltrados" />
      </v-col>
    </v-row>
  </v-container>
</template>
