<template>
    <Box>
        <div class="columns">
            <div class="column is-4">
                {{ tarefa.descricao || 'Tarefa sem descrição' }}
            </div>
            <div class="column is-3">
                {{ tarefa.projeto?.nome || 'N/D' }}
            </div>
            <div class="column">
                <Cronometro :tempoEmSegundos="tarefa.duracaoEmSegundos" />
            </div>
            <div class="column">
                <router-link :to="`/${tarefa.id}`" class="button">
                    <span class="icon is-small">
                        <i class="fas fa-pencil-alt"></i>
                    </span>
                </router-link>
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

import { computed, defineComponent, PropType } from 'vue'
import Cronometro from './Cronometro.vue'
import ITarefa from '../interfaces/ITarefa';
import Box from './Box.vue';
import { useStore } from '@/store';
import { EXCLUIR_TAREFA } from '@/store/tipo-mutacoes';
import useNotificador from '@/hooks/notificador';
import { TipoNotificacao } from '@/interfaces/INotificacao';

export default defineComponent({
    name: 'Tarefa',
    methods: {
        excluir(id: string) {
            this.store.commit(EXCLUIR_TAREFA, id)
            this.notificar(TipoNotificacao.ATENCAO, 'Tarefa Excluída', 'A tarefa foi excluída com sucesso!')
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
        return {
            store,
            notificar
        }
    }
})

</script>

<style scoped>
.box {
    background: #FAF0CA
}
</style>