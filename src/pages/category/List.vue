<template>
    <q-page padding>
        <div class="row">
            <q-table :rows="categories" :columns="columnsCategory" row-key="id" class="col-12" :loading="loading">
                <template v-slot:top>
                    <span class="text-h6">Categorias</span>

                    <q-space></q-space>
                    <q-btn v-if="$q.platform.is.desktop" label="Adiconar" color='primary' icon="mdi-plus"
                        :to="{ name: 'form-category' }">
                        <q-tooltip>Adicionar</q-tooltip>
                    </q-btn>

                </template>
                <template v-slot:body-cell-actions="props">
                    <q-td :props="props" class="q-gutter-x-sm">
                        <q-btn icon='mdi-pencil' color='info' dense flat
                            :to="{ name: 'form-category', params: { id: `${props.row.id}` } }">
                            <q-tooltip>Editar</q-tooltip>
                        </q-btn>
                        <q-btn icon='mdi-delete' color='negative' dense flat @click="handleDeleteCategory(props.row.id)">
                            <q-tooltip>Deletar</q-tooltip>
                        </q-btn>
                    </q-td>
                </template>
            </q-table>
        </div>

        <q-page-sticky position="bottom-right" :offset="[18, 18]">
            <q-btn v-if="$q.platform.is.mobile" color='primary' icon="mdi-plus" fab :to="{ name: 'form-category' }" />
        </q-page-sticky>
    </q-page>
</template>

<script>

import { defineComponent, ref, onMounted } from 'vue';
import useApi from 'src/composables/UseApi.js'
import useNotify from 'src/composables/useNotify';
import { useQuasar } from 'quasar'
import { columnsCategory } from './table'

export default defineComponent({
    name: 'PageCategoryList',

    setup() {
        const categories = ref([])
        const loading = ref(true)
        const { list, remove } = useApi()
        const { notifyError, notifySuccess } = useNotify()
        const $q = useQuasar()

        const handleListCategories = async () => {
            try {
                loading.value = true
                categories.value = await list('category')
                loading.value = false
            } catch (error) {
                notifyError(error.message)
            }
        }

        const handleDeleteCategory = async (id) => {
            try {
                $q.dialog({
                    title: 'Excluir',
                    message: 'Deseja realmente excluir?',
                    cancel: true,
                    persistent: true,
                }).onOk(async () => {
                    await remove('category', id)
                    notifySuccess('Exclusão realizada com sucesso !')
                    handleListCategories()
                })
            } catch (error) {
                notifyError(error.message)
            }
        }

        onMounted(() => {
            handleListCategories()
        })

        return {
            columnsCategory,
            categories,
            loading,
            handleDeleteCategory,
        }
    }
})
</script>