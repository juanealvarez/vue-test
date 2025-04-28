<template>
  <div>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/micasino.png" class="logo vue" alt="Vue logo" />
    </a>
  </div>
  <FormComponent :fieldDefinitions="fieldDefinitions" :disabledOptions="disabledOptions" @formSubmit="handleFormSubmit" />
</template>

<script setup lang="ts">
import { ref } from 'vue';
import FormComponent from './components/FormComponent.vue';

let fieldDefinitions = [
  {
    id: 'name',
    label: 'Nombre',
    type: 'Text',
    required: true,
  },
  {
    id: 'options',
    label: 'Options',
      type: 'SelectUnselect',
      options: [
        {
          id: '1',
          label: 'Option 1',
        },
        {
          id: '2',
          label: 'Option 2',
        },
        {
          id: '3',
          label: 'Option 3',
        },
      ],
    required: true,
  }
];

const disabledOptions = ref<string[]>([]);

function handleFormSubmit(formData: any) {
  if (Array.isArray(formData.options)) {
    // Agrega los nuevos ids seleccionados a la lista de deshabilitados, evitando duplicados
    disabledOptions.value = Array.from(new Set([...disabledOptions.value, ...formData.options]));
  }
}
</script>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #e3bd00aa);
}
</style>
