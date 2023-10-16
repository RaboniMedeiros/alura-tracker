<template>
    <router-view></router-view>
    <ListaTarefas />
</template>
  
<script lang="ts">
import { defineComponent } from 'vue';
import ListaTarefas from './Tarefas/ListaTarefas.vue'

/* eslint-disable vue/multi-word-component-names */

export default defineComponent({
    name: 'App',
    components: {
        ListaTarefas
    }
});
</script>
  