<template>
  <v-app>
    <v-app-bar app>
      <v-app-bar-nav-icon
        variant="text"
        @click.stop="drawer = !drawer"
      ></v-app-bar-nav-icon>
    </v-app-bar>

    <v-main>
      <v-navigation-drawer v-model="drawer" temporary>
        <p class="text-md-center">Opciones</p>
        <v-divider></v-divider>
        <v-list density="compact" nav>
          <v-list-item
            prepend-icon="mdi-view-dashboard"
            title="Home"
            value="home"
            @click="insertarRuta('/imagen')"
          ></v-list-item>
          <v-list-item
            prepend-icon="mdi-table"
            title="Tabla"
            value="tabla"
            @click="insertarRuta('/tabla')"
          ></v-list-item>
          <v-list-item
            prepend-icon="mdi-table"
            title="Tabla Update"
            value="updatetabla"
            @click="insertarRuta('/updatetabla')"
          ></v-list-item>
        </v-list>
      </v-navigation-drawer>

      <v-container>
        <router-view></router-view>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      drawer: null,
    };
  },
  methods: {
    insertarRuta(ruta) {
      this.$router.push(ruta);
    },
  },
};
</script>


