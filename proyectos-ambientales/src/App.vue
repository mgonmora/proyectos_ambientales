<script setup>
import Header from './components/header/Header.vue'
import Dashboard from './views/Dashboard.vue'
</script>

<template>
  <v-app>
    <v-main class="bg-teal-darken-3">
      <v-container class="pb-0">
        <Header />
      </v-container>
      <v-container class="text-center">
        <Dashboard />
      </v-container>
    </v-main>
  </v-app>
</template>
