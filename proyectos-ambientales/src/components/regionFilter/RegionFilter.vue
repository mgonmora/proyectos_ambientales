<script setup>
import { ref, computed, watch } from "vue";

const props = defineProps({
  proyectos: {
    type: Array,
    default: () => [],
  },
  modelValue: {
    type: String,
    default: "",
  },
});

const emit = defineEmits(["update:modelValue"]);

const regionesUnicas = computed(() => {
  const regiones = props.proyectos
    .map((p) => p.region)
    .filter((r) => r && r.trim() !== "");
  return [...new Set(regiones)];
});

const opcionesRegiones = computed(() => [
  { text: '-- Todas las regiones --', value: '' },
  ...regionesUnicas.value.map((r) => ({ text: r, value: r })),
]);

const seleccion = ref(props.modelValue || "");

watch(() => props.modelValue, (newVal) => {
  seleccion.value = newVal || "";
});

watch(seleccion, (newVal) => {
  emit("update:modelValue", newVal);
});
</script>

<template>
  <v-select
    :items="opcionesRegiones"
    label="Seleccione región"
    v-model="seleccion"
    clearable
    item-title="text"
    item-value="value"
    :menu-props="{ maxHeight: '300' }"
    variant="outlined"
    dense
  />
</template>
