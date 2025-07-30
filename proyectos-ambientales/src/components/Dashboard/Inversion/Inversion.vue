<script setup>
// Aquí importas composables o props si necesitas
</script>

<template>
  <v-card class="pa-4" elevation="2">
    <v-card-title class="text-h6">Título del componente</v-card-title>

    <v-card-text>
      <p>Este es un componente de ejemplo listo para editar con Vuetify 3.</p>

      <v-btn color="primary" class="mt-4" @click="console.log('Click!')">
        <v-icon start>mdi-check</v-icon>
        Acción
      </v-btn>
    </v-card-text>
  </v-card>
</template>