<template>
    <div class="lista">
        <Box v-if="listaEstaVazia">
            Você não está muito produtivo hoje :(
        </Box>
        <div class="field">
            <p class="control has-icons-left has-icons-right">
                <input class="input" type="text" placeholder="Digite para filtrar" v-model="filtro">
                <span class="icon is-small is-left">
                    <i class="fas fa-search"></i>
                </span>
            </p>
        </div>
        <Tarefa v-for="tarefa in tarefas" :key="tarefa.id" :tarefa="tarefa" @aoTarefaClicada="selecionarTarefa" />
        <Modal :mostrar="tarefaSelecionada != null">
            <header class="modal-card-head">
                <p class="modal-card-title">Editando uma tarefa</p>
                <button class="delete" aria-label="close" @click="fecharModal"></button>
            </header>
            <section class="modal-card-body">
                <div class="field">
                    <label for="descricaoDaTarefa" class="label">
                        Descrição
                    </label>
                    <input type="text" class="input" v-model="tarefaSelecionada.descricao" id="descricaoDaTarefa" />
                </div>
            </section>
            <footer class="modal-card-foot">
                <button class="button is-success" @click="alterarTarefa">Salvar Alterações</button>
                <button class="button" @click="fecharModal">Cancelar</button>
            </footer>
        </Modal>
    </div>
</template>

<script lang="ts">
import { defineComponent, computed, ref, watchEffect } from "vue";
import { useStore } from "@/store"
import Tarefa from '@/components/Tarefa.vue';
import Box from '@/components/Box.vue';
import Modal from '@/components/Modal.vue';
import { ALTERAR_TAREFA, OBTER_PROJETOS, OBTER_TAREFAS } from "@/store/tipo-acoes";
import ITarefa from "@/interfaces/ITarefa";

/* eslint-disable vue/multi-word-component-names */

export default defineComponent({
    name: 'Lista',
    components: {
        Tarefa,
        Box,
        Modal
    },
    data() {
        return {
            tarefaSelecionada: null as ITarefa | null
        }
    },
    methods: {
        selecionarTarefa(tarefa: ITarefa) {
            this.tarefaSelecionada = tarefa
        },
        fecharModal() {
            this.tarefaSelecionada = null
        },
        alterarTarefa() {
            this.store.dispatch(ALTERAR_TAREFA, this.tarefaSelecionada)
                .then(() => this.fecharModal())
        }
    },
    computed: {
        listaEstaVazia(): boolean {
            //return this.tarefas.length === 0
            return false
        }
    },
    setup() {
        const store = useStore()
        store.dispatch(OBTER_TAREFAS)
        store.dispatch(OBTER_PROJETOS)
        //const tarefas = computed(() =>
        //    store.state.tarefa.tarefas.filter(
        //        (t) => !filtro.value || t.descricao.includes(filtro.value)
        //    )
        //);
        const filtro = ref("")

        watchEffect(() => {
            store.dispatch(OBTER_TAREFAS, filtro.value)
        })

        return {
            tarefas: computed(() => store.state.tarefa.tarefas),
            store,
            filtro
        }
    }
})
</script>
