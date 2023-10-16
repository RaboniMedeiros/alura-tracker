<template>
    <div class="lista">
        <Tarefa v-for="tarefa in tarefas" :key="tarefa.id" :tarefa="tarefa" />
        <Box v-if="listaEstaVazia">
            Você não está muito produtivo hoje :(
        </Box>
    </div>
</template>

<script lang="ts">
import { defineComponent, computed } from "vue";
import { useStore } from "@/store"
import Tarefa from '@/components/Tarefa.vue';
import Box from '@/components/Box.vue';

/* eslint-disable vue/multi-word-component-names */

export default defineComponent({
    name: 'Lista',
    components: {
        Tarefa,
        Box
    },
    computed: {
        listaEstaVazia(): boolean {
            return this.tarefas.length === 0
        }
    },
    setup() {
        const store = useStore()
        return {
            tarefas: computed(() => store.state.tarefas),
            store
        }
    }
})
</script>
