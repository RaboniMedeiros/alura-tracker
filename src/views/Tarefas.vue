<template>
    <Formulario />
    <ListaTarefas />
</template>
  
<script lang="ts">
import { defineComponent } from 'vue';
import ListaTarefas from './Tarefas/ListaTarefas.vue'
import Formulario from './Tarefas/Formulario.vue';

/* eslint-disable vue/multi-word-component-names */

export default defineComponent({
    name: 'App',
    components: {
        ListaTarefas,
        Formulario
    }
});
</script>
  