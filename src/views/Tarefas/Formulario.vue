<template>
    <div class="box formulario">
        <div class="columns">
            <div class="column is-5" role="form" aria-label="Formulário para criação de uma nova tarefa">
                <input type="text" class="input" placeholder="Qual tarefa você deseja iniciar?" v-model="descricao"
                    id="descricao">
            </div>
            <div class="column is-3">
                <div class="select">
                    <select v-model="idProjeto">
                        <option value="">Selecione o projeto</option>
                        <option :value="projeto.id" v-for="projeto in projetos" :key="projeto.id">
                            {{ projeto.nome }}
                        </option>
                    </select>
                </div>
            </div>
            <div class="column">
                <Temporizador @aoTemporizadorFinalizado="finalizarTarefa" />
            </div>
        </div>
    </div>
</template>

<script lang="ts">

import { computed, defineComponent, ref } from 'vue';
import Temporizador from '@/components/Temporizador.vue';
import { useStore } from 'vuex';
import ITarefa from '@/interfaces/ITarefa'
import { key } from '@/store'
import { TipoNotificacao } from '@/interfaces/INotificacao';
import useNotificador from '@/hooks/notificador';
import { CADASTRAR_TAREFA } from '@/store/tipo-acoes';
import { useRouter } from 'vue-router';

/* eslint-disable vue/multi-word-component-names */

export default defineComponent({
    name: 'Formulario',
    components: {
        Temporizador
    },
    setup() {
        const router = useRouter();
        const store = useStore(key);
        const projetos = computed(() => store.state.projeto.projetos)
        const { notificar } = useNotificador();
        const descricao = ref("");
        const idProjeto = ref("");

        const finalizarTarefa = (tempoDecorrido: number) => {
            if (idProjeto.value) {
                const tarefa = {
                    id: new Date().getTime(),
                    duracaoEmSegundos: tempoDecorrido,
                    descricao: descricao.value,
                    projeto: projetos.value.find(proj => proj.id == idProjeto.value)
                } as ITarefa
                store.dispatch(CADASTRAR_TAREFA, tarefa)
                    .then(() => {
                        lidarComSucesso(TipoNotificacao.SUCESSO, 'Excelente!', 'A tarefa foi cadastrada com sucesso!')
                    })
            } else {
                notificar(TipoNotificacao.FALHA, 'Tarefa sem projeto vinculado', 'Não há projeto vinculado a esta tarefa! Tente novamente!')
            }
        }

        const lidarComSucesso = (TipoNotificacao: TipoNotificacao, titulo: string, texto: string) => {
            descricao.value = '';
            router.push('/')
            notificar(TipoNotificacao, titulo, texto)
        }

        return {
            descricao,
            idProjeto,
            projetos,
            finalizarTarefa
        }
    }
});

</script>

<style>
.formulario {
    color: var(--texto-primario);
    background-color: var(--bg-primario);
}
</style>