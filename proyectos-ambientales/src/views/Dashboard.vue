<script setup>
import IngresadosAprobados from '../components/Dashboard/IngresadosAprobados/IngresadosAprobados.vue'
import Inversion from '../components/Dashboard/Inversion/Inversion.vue'
import TiempoTramitacion from '../components/Dashboard/TiempoTramitacion/TiempoTramitacion.vue'
</script>

<template>
  <v-container class="py-6">
    <h1 class="text-h5 mb-4">Dashboard</h1>

    <v-row dense>
      <v-col cols="12" md="4">
        <IngresadosAprobados />
      </v-col>
      <v-col cols="12" md="4">
        <Inversion />
      </v-col>
      <v-col cols="12" md="4">
        <TiempoTramitacion />
      </v-col>
    </v-row>
  </v-container>
</template>
