<template>
  <v-data-table :headers="headers" :items="registros" :sort-by="[{ key: 'titulo', order: 'asc' }]" class="elevation-1"
    :items-per-page-text="'Elementos por página'">
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Tabla de registros</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
        <v-spacer></v-spacer>
        <v-btn color="primary" dark class="mb-2" @click="fetchData">
          Reiniciar
        </v-btn>
        <v-dialog v-model="dialog" max-width="500px">
          <template v-slot:activator="{ props }">
            <v-btn color="primary" dark class="mb-2" v-bind="props">
              Añadir
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.name" label="Nombre"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.titulo" label="Titulo"></v-text-field>
                  </v-col>
                  <v-col cols="12" sm="6" md="4">
                    <v-text-field v-model="editedItem.contenido" label="Contenido"></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="close">
                Cancelar
              </v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="save">
                Guardar
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-center">¿Seguro que quieres eliminar el elemento?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-darken-1" variant="text" @click="closeDelete">Cancelar</v-btn>
              <v-btn color="blue-darken-1" variant="text" @click="deleteItemConfirm">Eliminar</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon size="small" class="me-2" @click="editItem(item.raw)">
        mdi-pencil
      </v-icon>
      <v-icon size="small" @click="deleteItem(item.raw)">
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn color="primary" @click="fetchData">
        Reiniciar
      </v-btn>
    </template>
  </v-data-table>
</template>

<script>
export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    headers: [
      {
        title: 'Nombre',
        align: 'start',
        sortable: false,
        key: 'name',
      },
      { title: 'Titulo', key: 'titulo' },
      { title: 'Contenido', key: 'contenido' },
      { title: 'Acciones', key: 'actions', sortable: false },
    ],
    registros: [],
    editedIndex: -1,
    editedItem: {
      name: '',
      titulo: '',
      contenido: '',
    },
    defaultItem: {
      name: '',
      titulo: '',
      contenido: '',
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'Añadir elemento' : 'Editar elemento'
    },
  },

  watch: {
    dialog(val) {
      val || this.close()
    },
    dialogDelete(val) {
      val || this.closeDelete()
    },
  },

  created() {
    this.fetchData();
  },

  methods: {
    async fetchData() {
      const usersResponse = await fetch('https://jsonplaceholder.typicode.com/users');
      const usersData = await usersResponse.json();

      const postsResponse = await fetch('https://jsonplaceholder.typicode.com/posts');
      const postsData = await postsResponse.json();

      this.registros = [];

      usersData.forEach((user) => {
        const userPosts = postsData.filter((post) => post.userId === user.id);
        userPosts.forEach((post) => {
          this.registros.push({
            name: user.name,
            titulo: post.title,
            contenido: post.body,
          });
        });
      });

      console.log(this.registros);
    },

    editItem(item) {
      this.editedIndex = this.registros.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },

    deleteItem(item) {
      this.editedIndex = this.registros.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialogDelete = true
    },

    deleteItemConfirm() {
      this.registros.splice(this.editedIndex, 1)
      this.closeDelete()
    },

    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    closeDelete() {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.registros[this.editedIndex], this.editedItem)
      } else {
        this.registros.push(this.editedItem)
      }
      this.close()
    },
  },
}
</script>