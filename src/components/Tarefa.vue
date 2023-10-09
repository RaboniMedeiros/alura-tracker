<template>
    <Box>
        <div class="columns">
            <div class="column is-7">
                {{ tarefa.descricao || 'Tarefa sem descrição' }}
            </div>
            <div class="column">
                <Cronometro :tempoEmSegundos="tarefa.duracaoEmSegundos" />
            </div>
        </div>
    </Box>
</template>

<script lang="ts">

/* eslint-disable vue/multi-word-component-names */

import { defineComponent, PropType } from 'vue'
import Cronometro from './Cronometro.vue'
import ITarefa from '../interfaces/ITarefa';
import Box from './Box.vue';

export default defineComponent({
    name: 'Tarefa',
    components: {
        Cronometro,
        Box
    },
    props: {
        tarefa: {
            type: Object as PropType<ITarefa>,
                required: true
        }
    }
})

</script>

<style scoped>
.box {
    background: #FAF0CA
}
</style>