<template>
  <v-app id="inspire">
    <v-navigation-drawer
      v-model="drawer"
      v-if="!isLogin"
      app>
      <v-list dense>
        <v-list-item>
          <v-list-item-action>
            <v-icon>mdi-account-multiple-plus</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>
              <router-link to="/newuser">Register user</router-link>
            </v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        <v-list-item>
          <v-list-item-action>
            <v-icon>mdi-account-box</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>
              <router-link to="/users">List users</router-link>
            </v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        <v-list-item>
        <v-list-item-action>
          <v-icon>mdi-certificate</v-icon>
        </v-list-item-action>
        <v-list-item-content>
          <v-list-item-title>
            <router-link to="/certificado">Check certificate</router-link>
          </v-list-item-title>
        </v-list-item-content>
        </v-list-item>

      </v-list>
    </v-navigation-drawer>

    <v-app-bar
      app
      color="blue"
      dark
      v-if="!isLogin">
      <v-app-bar-nav-icon
        @click.stop="drawer = !drawer"
        v-if="!isLogin"
      ></v-app-bar-nav-icon>
      <v-toolbar-title>Frontend</v-toolbar-title>

      <v-spacer></v-spacer>

      {{ $session.get('email') }}

      <v-app-bar-nav-icon @click="logout">
        <v-tooltip bottom>
          <template v-slot:activator="{ on, attrs }">
            <v-icon
              dark
              v-bind="attrs"
              v-on="on">
              mdi-logout
            </v-icon>
          </template>
          <span>Logout</span>
        </v-tooltip>

      </v-app-bar-nav-icon>
    </v-app-bar>

    <v-main :class="isLogin ? 'bg' : ''">
      <v-container
        fluid>
        <v-row>
          <v-col>
            <router-view/>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
    <v-footer
      color="blue"
      app
      v-if="!isLogin">
      <span class="white--text">Henzo Gomes 🚀🚀</span>
      <v-spacer></v-spacer>
      <span class="white--text">{{ new Date().getFullYear() }}</span>
    </v-footer>

    <Loading ref="loading"/>
  </v-app>
</template>

<script>
import {events} from './plugins/events'
import Loading from "@/components/Loading";

export default {
  components: {Loading},
  props: {
    source: String,
  },

  data: () => ({
    drawer: false,
  }),
  computed: {
    login: function () {
      return (this.$session.get('login'))
    },
    currentRouteName() {
      return this.$route.name;
    },
    isLogin() {
      return this.currentRouteName === 'login'
    }
  },
  mounted() {
    const loading = this.$refs.loading

    // toda vez que o axios disparar esses eventos, deve mostrar o loading na tela
    events.$on(['loadingStart', 'httpStart'], () => loading.start())
    events.$on(['loadingDone', 'httpDone'], () => loading.done())
  },
  methods: {
    logout() {
      this.$confirm("Do you wish to log out?", "", "",
        {
          confirmButtonText: 'Yes',
          cancelButtonText: 'No'
        }).then(() => {
        this.$router.replace("/logout")
      });
    }
  },
  watch: {
    $route: {
      immediate: true,
      handler(to) {
        document.title = to.meta.title || 'Frontend';
      }
    },
  }
}
</script>


<style scoped>
.bg {
  background-image: url("assets/bg1.jpg");
  background-size: cover;
}
</style>

<style>
.swal2-content, .swal2-actions {
  font-family: Arial, Helvetica, sans-serif;
}
</style>
