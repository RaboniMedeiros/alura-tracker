<template>
    <section>
        <form @submit.prevent="salvar">
            <div class="field">
                <label for="nomeDoProjeto" class="label">
                    Nome do Projeto
                </label>
                <input type="text" class="input" v-model="nomeDoProjeto" id="nomedoProjeto" />
            </div>
            <div class="field">
                <button class="button" type="submit">
                    Salvar
                </button>
            </div>
        </form>
    </section>
</template>

<script lang="ts">
import { useStore } from "@/store";
import { defineComponent, ref } from "vue";
import { TipoNotificacao } from "@/interfaces/INotificacao";
import useNotificador from '@/hooks/notificador'
import { ALTERAR_PROJETO, CADASTRAR_PROJETO } from "@/store/tipo-acoes";
import { useRouter } from "vue-router";

/* eslint-disable vue/multi-word-component-names */

export default defineComponent({
    name: 'Formulario',
    props: {
        id: {
            type: String
        }
    },
    setup(props) {
        const router = useRouter();
        const store = useStore();
        const { notificar } = useNotificador();
        const nomeDoProjeto = ref("")

        if (props.id) {
            const projeto = store.state.projeto.projetos.find(proj => proj.id == props.id)
            nomeDoProjeto.value = projeto?.nome || ''
        }

        const lidarComSucesso = (TipoNotificacao: TipoNotificacao, titulo: string, texto: string) => {
            nomeDoProjeto.value = '';
            notificar(TipoNotificacao, titulo, texto)
            router.push('/projetos')
        }

        const salvar = () => {
            if (props.id) {
                store.dispatch(ALTERAR_PROJETO, {
                    id: props.id,
                    nome: nomeDoProjeto.value
                }).then(() => lidarComSucesso(TipoNotificacao.SUCESSO, 'Excelente!', 'O projeto foi alterado com sucesso!'))
            } else {
                store
                    .dispatch(CADASTRAR_PROJETO, nomeDoProjeto.value)
                    .then(() => lidarComSucesso(TipoNotificacao.SUCESSO, 'Excelente!', 'O projeto foi cadastrado com sucesso!'))
            }
        }

        return {
            nomeDoProjeto,
            salvar
        }
    }
})
</script>
