<template>
    <Box>
        <div class="columns">
            <div class="column is-4 clicavel" @click="tarefaClicada">
                {{ tarefa.descricao || 'Tarefa sem descrição' }}
            </div>
            <div class="column is-3">
                {{ tarefa.projeto?.nome || 'N/D' }}
            </div>
            <div class="column">
                <Cronometro :tempoEmSegundos="tarefa.duracaoEmSegundos" />
            </div>
            <div class="column">
                <button class="button ml-2 is-danger" @click="excluir(tarefa.id)">
                    <span class="icon is-small">
                        <i class="fas fa-trash"></i>
                    </span>
                </button>
            </div>
        </div>
    </Box>
</template>

<script lang="ts">

/* eslint-disable vue/multi-word-component-names */

import { defineComponent, PropType, ref } from 'vue'
import Cronometro from './Cronometro.vue'
import ITarefa from '../interfaces/ITarefa';
import Box from './Box.vue';
import { useStore } from '@/store';
import useNotificador from '@/hooks/notificador';
import { TipoNotificacao } from '@/interfaces/INotificacao';
import { REMOVER_TAREFA } from '@/store/tipo-acoes';

export default defineComponent({
    name: 'Tarefa',
    emits: ['aoTarefaClicada'],
    methods: {
        tarefaClicada (): void {
            this.$emit('aoTarefaClicada', this.tarefa)
        }
    },
    components: {
        Cronometro,
        Box
    },
    props: {
        tarefa: {
            type: Object as PropType<ITarefa>,
            required: true
        }
    },
    setup() {
        const store = useStore()
        const { notificar } = useNotificador()

        const excluir = (id: number) => {
            store
                .dispatch(REMOVER_TAREFA, id)
                .then(() => {
                    notificar(TipoNotificacao.ATENCAO, 'Tarefa Excluída', 'A tarefa foi excluída com sucesso!')
                })
        }

        return {
            store,
            notificar,
            excluir
        }
    }
})

</script>

<style scoped>
.clicavel {
    cursor: pointer
}
</style>