<script>
/* eslint-disable */
import axios from 'axios';

export default {
    data(){
        return {
            consolas: {},
            dialog: false,
            dialogDelete: false,
            headers: [
                {
                    title: 'Nombre',
                    align: 'start',
                    sortable: false,
                    key: 'nombre',
                },
                { title: 'Logo', align: 'center', sortable: false, key: 'img'},
                { title: '# de juegos', key: 'nojuegos'},
                { title: 'Descripción', key: 'descripcion'},
                { title: 'Acciones', key: 'actions', sortable: false}
            ],
            editedIndex: -1,
            editedItem: {
                idconsola: 0,
                nombre: '',
                nojuegos: 0,
                descripcion: '',
            },
            defaultItem: {
                idconsola: 0,
                nombre: '',
                nojuegos: 0,
                descripcion: '',
            },
            alert: false,
            textoAlert: "",
            tipoAlert: '',
        }
    },
    mounted(){this.cargaTablaConsolas()},
    methods: {
        cargaTablaConsolas: function(){
            axios.get("/api/consolas").then((res) => {
                this.consolas = res.data.data;
            })
        },
        initialize(){
            axios.get("/api/consolas").then((res) => {
                this.consolas = res.data.data;
            })
        },
        editItem(item){
            this.editedIndex = this.consolas.indexOf(item)
            this.editedItem = {...item}
            this.dialog = true
        },
        deleteItem(item){
          this.editedIndex = this.consolas.indexOf(item)
          this.editedItem = {...item}
          this.dialogDelete = true
        },
        deleteItemConfirm(){
          this.consolas.splice(this.editedIndex, 1)
          this.closeDelete()
        },
        close(){
          this.dialog = false
          this.$nextTick(() => {
            this.editedItem = {...this.defaultItem}
            this.editedIndex = -1
          })
        },
        closeDelete(){
          this.dialogDelete = false
          this.$nextTick(() => {
            this.editedItem = {...this.defaultItem}
            this.editedIndex = -1
          })
        },
        save(){
          if(this.editedIndex > -1){
            //Object.assign(this.consolas[this.editedIndex], this.editedItem)
            axios.post("/api/editar_consola", {
              id: this.editedItem.idconsola,
              nombre: this.editedItem.nombre,
              nojuegos: this.editedItem.nojuegos,
              descripcion: this.editedItem.descripcion
            }).then((res) => {
              this.textoAlert = "Se editó la consola"
              this.tipoAlert = 'success'
              this.alert = true
              this.cargaTablaConsolas();
            }).catch(() => {
              this.textoAlert = "Error al editar"
              this.tipoAlert = 'error'
              this.alert = true
            })
          }else{
            //this.consolas.push(this.editedItem)
          }
          this.close()
        },
    },
    computed: {
        formTitle () {
            return this.editedIndex === -1 ? 'Nuevo' : 'Editar'
        },
    },
    watch: {
        dialog (val) {
            val || this.close()
        },
        dialogDelete (val) {
            val || this.closeDelete()
        },
    },

    created() { this.initialize()}
}
</script>

<template>
  <div>
    <v-alert
    v-model="alert"
    :type="tipoAlert"
    closable>{{ textoAlert }}</v-alert>
  </div>
<v-data-table
:headers="headers"
:items="consolas"
:sort-by="[{ key: 'nojuegos', order: 'desc' }]">
  <template v-slot:top>
    <v-toolbar flat>
      <v-toolbar-title>Consolas</v-toolbar-title>
      <v-divider class="mx-4" inset vertical></v-divider>
      <v-spacer></v-spacer>
      <v-dialog v-model="dialog" min-width="500px" max-width="940px">
        <template v-slot:activator="{ props }">
          <v-btn class="mb-2" color="primary" dark v-bind="props">Nuevo</v-btn>
        </template>
        <v-card>
          <v-card-title>
            <span class="text-h5">{{ formTitle }}</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12" md="4" sm="6">
                  <v-text-field v-model="editedItem.nojuegos"
                  label="# de Juegos"></v-text-field>
                </v-col>
                <v-col cols="12" md="4" sm="6">
                  <v-text-field v-model="editedItem.descripcion"
                  label="Descripción"></v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
            color="blue-darken-1"
            variant="text"
            @click="close">Cancelar</v-btn>
            <v-btn
            color="blue-darken-1"
            variant="text"
            @click="save">
            Guardar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-toolbar>
  </template>
  <template v-slot:item.img="{ item }">
    <v-card class="my-2" elevation="2" rounded>
      <v-img
        :src="`src/assets/${item.img}`"
        height="64"
        color="white"
      ></v-img>
    </v-card>
  </template>
  <template v-slot:item.actions="{ item }">
    <v-icon
    class="me-2"
    size="small"
    @click="editItem(item)">mdi-pencil</v-icon>
  </template>
  <template v-slot:no-data>
    <v-btn color="primary" @click="initialize">Reset</v-btn>
  </template>
</v-data-table>
</template>
