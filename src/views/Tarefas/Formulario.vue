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

import { computed, defineComponent } from 'vue';
import Temporizador from '@/components/Temporizador.vue';
import { useStore } from 'vuex';
import ITarefa from '@/interfaces/ITarefa'
import { key } from '@/store'
import { ADICIONA_TAREFA, ALTERA_TAREFA, NOTIFICAR } from '@/store/tipo-mutacoes';
import { TipoNotificacao } from '@/interfaces/INotificacao';
import useNotificador from '@/hooks/notificador';

/* eslint-disable vue/multi-word-component-names */

export default defineComponent({
    name: 'Formulario',
    props: {
        id: {
            type: String
        }
    },
    mounted() {
        if (this.id) {
            const tarefa = this.store.state.tarefas.find(tar => tar.id == this.id)
            this.descricao = tarefa?.descricao || ''
        }
    },
    components: {
        Temporizador
    },
    data() {
        return {
            descricao: '',
            idProjeto: ''
        }
    },
    methods: {
        finalizarTarefa(tempoDecorrido: number): void {
            if (this.id) {
                const tarefa = {
                    id: this.id,
                    duracaoEmSegundos: tempoDecorrido,
                    descricao: this.descricao,
                    projeto: this.projetos.find(proj => proj.id == this.idProjeto)
                } as ITarefa
                this.store.commit(ALTERA_TAREFA, tarefa)
                this.notificar(TipoNotificacao.SUCESSO, 'Excelente!', 'A tarefa foi alterada com sucesso!')
                this.descricao = '';
                this.$router.push('/')
            } else {
                if (this.idProjeto) {
                    const tarefa = {
                        id: new Date().toISOString(),
                        duracaoEmSegundos: tempoDecorrido,
                        descricao: this.descricao,
                        projeto: this.projetos.find(proj => proj.id == this.idProjeto)
                    } as ITarefa
                    this.store.commit(ADICIONA_TAREFA, tarefa)
                    this.notificar(TipoNotificacao.SUCESSO, 'Excelente!', 'A tarefa foi cadastrada com sucesso!')
                    this.descricao = ''
                    this.$router.push('/')
                } else {
                    this.notificar(TipoNotificacao.FALHA, 'Tarefa sem projeto vinculado', 'Não há projeto vinculado a esta tarefa! Tente novamente!')
                }

            }
        }
    },
    setup() {
        const store = useStore(key)
        const { notificar } = useNotificador()
        return {
            projetos: computed(() => store.state.projetos),
            tarefas: computed(() => store.state.tarefas),
            store,
            notificar
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