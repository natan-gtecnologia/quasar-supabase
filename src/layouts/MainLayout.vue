<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn flat dense round icon="menu" aria-label="Menu" @click="toggleLeftDrawer" />

        <q-toolbar-title>
          Controle de Estoque
        </q-toolbar-title>

        <q-btn-dropdown flat color="white" icon="person">
          <q-list>
            <q-item clickable v-close-popup @click="handleLogout">
              <q-item-section>
                <q-item-label>Logout</q-item-label>
              </q-item-section>
            </q-item>
          </q-list>
        </q-btn-dropdown>

      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" show-if-above bordered>
      <q-list>
        <q-item-label header>
          Menu
        </q-item-label>

        <EssentialLink v-for="link in essentialLinks" :key="link.title" v-bind="link" />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from 'vue'
import EssentialLink from 'components/EssentialLink.vue'
import useAuthUser from 'src/composables/UseAuthUser'
import { useRouter } from 'vue-router'
import { useQuasar } from 'quasar'

const linksList = [
  {
    title: 'Home',
    icon: 'home',
    routeName: 'me'
  },
  {
    title: 'Categoria',
    icon: 'mdi-shape-outline',
    routeName: 'category'
  },
  {
    title: 'Produtos',
    icon: 'mdi-format-list-checkbox',
    routeName: 'products'
  },
]

export default defineComponent({
  name: 'MainLayout',

  components: {
    EssentialLink
  },

  setup() {
    const { logout } = useAuthUser()
    const router = useRouter()
    const leftDrawerOpen = ref(false)
    const $q = useQuasar()

    const handleLogout = async () => {
      $q.dialog({
        title: 'Logout',
        message: 'Deseja realmente sair?',
        cancel: true,
        persistent: true,
      }).onOk(async () => {
        await logout()
        router.replace({ name: 'login' })
      })
    }

    return {
      essentialLinks: linksList,
      leftDrawerOpen,
      handleLogout,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value
      }
    }
  }
})
</script>
