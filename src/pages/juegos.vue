<script>
/* eslint-disable */
import axios from 'axios';
import { ref } from 'vue';

export default {
  data() {
    return {
    juegos: {},
    consolas: {},
    plataformas: {},
    filtro: {
      nombre: "",
      consola: "",
      estado: "",
    },
    dialog: false,
    dialogDelete: false,
    dialogSesion: false,
    dialogDB: false,
    headers: [
      {
        title: 'Nombre',
        align: 'start',
        sortable: false,
        key: 'nombre'
      },
      { title: 'Consola', align: 'center', key: 'consola'},
      { title: 'Plataforma', align: 'center', key: 'plataforma'},
      {
        title: 'Fecha Adquirido',
        align: 'center',
        sortable: false,
        key: 'fecha_adquirido'
      },
      { title: 'Estado', align: 'center', key: 'estado'},
      {
        title: 'Fecha Completado',
        align: 'center',
        sortable: false,
        key: 'fecha_completado'
      },
      { title: 'Nota', align: 'start', sortable: false, key: 'nota'},
      { title: 'Acciones', key: 'actions', sortable: false},
    ],
    editedIndex: -1,
    editedItem: {
      idjuegos: 0,
      nombre: '',
      idconsola: 0,
      idplataforma: 0,
      fecha_adquirido_edit: null,
      estado: '',
      completado: 0,
      fecha_completado_edit: null,
      nota: '',
    },
    defaultItem: {
      idjuegos: 0,
      nombre: '',
      idconsola: 0,
      idplataforma: 0,
      fecha_adquirido_edit: null,
      estado: '',
      completado: 0,
      fecha_completado_edit: null,
      nota: '',
    },
    alert: false,
    textoAlert: "",
    tipoAlert: '',
    alertSesion: false,
    textoAlertSesion: "",
    tipoAlertSesion: '',
    enSesion: false,
    buscarJuego: "",
    juegoDB: "",
    opcionDB: ""
    }
  },
  mounted(){
    this.cargaTablaJuegos();
    this.cargaListadoConsola();
    this.cargaListadoPlataforma();
  },
  methods: {
    cargaTablaJuegos: function(){
            axios.get("/api/juegos").then((res) => {
                this.juegos = res.data.data;
            })
            .catch(() => {
                console.log("Algo anda mal con la tabla 'listado_juegos'.");
                alert("Algo anda mal con la tabla 'listado_juegos'");
            })
        },
    cargaListadoConsola: function(){
            axios.get("/api/lista_consolas").then((res) => {
                this.consolas = ref(res.data.data[0]);
            })
            .catch(() => {
                console.log("Algo anda mal con el procedimiento DD_Consolas");
                alert("Algo anda mal con el procedimiento DD_Consolas");
            })
        },
    cargaListadoPlataforma: function(){
            axios.get("/api/lista_plataformas").then((res) => {
                this.plataformas = ref(res.data.data[0]);
            })
            .catch(() => {
                console.log("Algo anda mal con el procedimiento DD_Plataformas");
                alert("Algo anda mal con el procedimiento DD_Plataformas");
            })
        },
        initialize(){
          this.cargaTablaJuegos();
          this.cargaListadoConsola();
          this.cargaListadoPlataforma();
        },
        editItem(item){
          this.editedIndex = this.juegos.indexOf(item)
          this.editedItem = {...item}
          this.dialog = true
        },
        sesionItem(item){
          this.editedIndex = this.juegos.indexOf(item)
          this.editedItem = {...item}
          this.dialogSesion = true
        },
        close(){
          this.dialog = false
          this.$nextTick(() => {
            this.editedItem = {...this.defaultItem}
            this.editedIndex = -1
          })
        },
        closeSesion(){
          this.dialogSesion = false
          this.$nextTick(() => {
            this.editedItem.nombre = {...this.defaultItem.nombre}
            this.editedIndex = -1
          })
        },
        closeSesionDB(){
          this.dialogDB = false
          this.$nextTick(() => {
            this.editedItem = {...this.defaultItem}
            this.editedIndex = -1
          })
        },
        save(){
          if(this.editedIndex > -1){
            axios.post("/api/editar_juego", {
              idjuego: this.editedItem.idjuegos,
              nombre: this.editedItem.nombre,
              idconsola: this.editedItem.idconsola,
              idplataforma: this.editedItem.idplataforma,
              fecha_adquirido: this.editedItem.fecha_adquirido_edit,
              estado: this.editedItem.estado,
              completado: this.editedItem.completado,
              fecha_completado: this.editedItem.fecha_completado_edit,
              nota: this.editedItem.nota
            }).then((res) => {
              this.tipoAlert = 'success'
              this.textoAlert = "Se editó el juego con éxito"
              this.alert = true
              this.cargaTablaJuegos();
            }).catch(() => {
              this.tipoAlert = 'error'
              this.textoAlert = "Error en la edición"
              this.alert = true
            })
          }else{
            axios.post("/api/juego_nuevo", {
              nombre: this.editedItem.nombre,
              idconsola: this.editedItem.idconsola,
              idplataforma: this.editedItem.idplataforma,
              fecha_adquirido: this.editedItem.fecha_adquirido_edit,
              estado: this.editedItem.estado,
              completado: this.editedItem.completado,
              fecha_completado: this.editedItem.fecha_completado_edit,
              nota: this.editedItem.nota
            }).then((res) => {
              this.tipoAlert = 'success'
              this.textoAlert = "Se registro el juego con éxito"
              this.alert = true
              this.cargaTablaJuegos();
            }).catch(() => {
              this.tipoAlert = 'error'
              this.textoAlert = "Error en el registro"
              this.alert = true
            })

          }
          this.close()
        },
        inicioSesion(){
          if(this.editedIndex > -1){
            axios.post("/api/inicio_sesion", {
              id: this.editedItem.idjuegos,
              juego: this.editedItem.nombre,
              idconsola: this.editedItem.idconsola,
              idplataforma: this.editedItem.idplataforma,
              demo: ""
            }).then((res) => {
              this.alertSesion = true
              this.textoAlertSesion = "Sesión iniciada"
              this.tipoAlertSesion = 'success'
              this.enSesion = true
            }).catch(() => {
              this.alertSesion = true
              this.textoAlertSesion = "Error al iniciar sesión, intente de nuevo"
              this.tipoAlertSesion = 'error'
            })
          }
        },
        finSesion(){
          if(this.editedIndex > -1){
            axios.post("/api/fin_sesion", {
              id: this.editedItem.idjuegos,
              juego: this.editedItem.nombre
            }).then((res) => {
              this.alertSesion = true
              this.textoAlertSesion = "Ha finalizado la sesión"
              this.tipoAlertSesion = 'success'
              this.enSesion = false
            }).catch(() => {
              this.alertSesion = true
              this.textoAlertSesion = "Error al finalizar la sesión, revise la base de datos e intente de nuevo"
              this.tipoAlertSesion = 'error'
            })
          }
        },
        inicioSesionDB(){
          if(this.editedItem.nombre != ""){
            if(this.editedItem.idjuegos === 0){
              this.opcionDB = "Demo"
            }else{
              this.opcionDB = "Beta"
            }
            axios.post("/api/inicio_sesion", {
              id: this.editedItem.idjuegos,
              juego: this.opcionDB,
              idconsola: this.editedItem.idconsola,
              idplataforma: this.editedItem.idplataforma,
              demo: this.editedItem.nombre
            }).then((res) => {
              this.alertSesion = true
              this.textoAlertSesion = "Sesión iniciada"
              this.tipoAlertSesion = 'success'
              this.enSesion = true
            }).catch(() => {
              this.alertSesion = true
              this.textoAlertSesion = "Error al iniciar sesión, intente de nuevo"
              this.tipoAlertSesion = 'error'
            })
          }
        },
        finSesionDB(){
          if(this.editedItem.nombre != ""){
            if(this.editedItem.idjuegos === 0){
              this.opcionDB = "Demo"
            }else{
              this.opcionDB = "Beta"
            }
            axios.post("/api/fin_sesion", {
              id: this.editedItem.idjuegos,
              juego: this.opcionDB
            }).then((res) => {
              this.alertSesion = true
              this.textoAlertSesion = "Ha finalizado la sesión"
              this.tipoAlertSesion = 'success'
              this.enSesion = false
            }).catch(() => {
              this.alertSesion = true
              this.textoAlertSesion = "Error al finalizar la sesión, revise la base de datos e intente de nuevo"
              this.tipoAlertSesion = 'error'
            })
          }
        },
        filtroNombreJuego(item){
          return item.nombre.toLowerCase().includes(this.filtro.nombre.toLowerCase());
        },
        filtroConsolaJuego(item){
          return item.consola.toLowerCase().includes(this.filtro.consola.toLowerCase());
        },
        filtroEstadoJuego(item){
          return item.estado.toLowerCase().includes(this.filtro.estado.toLowerCase());
        }
  },
  computed: {
    formTitle(){
      return this.editedIndex === -1 ? 'Nuevo' : 'Editar'
    },
    siCompletado(){
      return this.editedItem.completado === 1 ? true : false
    },
  },
  watch: {
    dialog(val){
      val || this.close()
    },
    dialogSesion(val){
      val || this.close()
    },
    dialogDB(val){
      val || this.close()
    }
  },
  created(){this.initialize()}
}
</script>

<template>
  <div style="align-items: center;">
    <v-alert
    v-model="alert"
    :type="tipoAlert"
    closable>{{ textoAlert }}</v-alert>
  </div>
<v-data-table
:headers="headers"
:items="juegos"
:filter-keys="['nombre']"
:search="buscarJuego"
:sort-by="[{ key: 'idjuegos', order: 'asc' }]">
  <template v-slot:top>
    <v-toolbar flat>
      <v-toolbar-title>Juegos</v-toolbar-title>
      <v-divider class="mx-4" inset vertical></v-divider>
      <v-spacer></v-spacer>
      <v-text-field
        v-model="buscarJuego"
        density="compact"
        label="Buscar Juego..."
        prepend-inner-icon="mdi-magnify"
        variant="solo-filled"
        flat
        hide-details
        single-line
      ></v-text-field>
      <v-spacer></v-spacer>
      <v-dialog v-model="dialog" min-width="1000px" max-width="1540px">
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
                <v-text-field v-model="editedItem.nombre"
                label="Nombre"></v-text-field>
              </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" md="4" sm="6">
                  <v-select
                  v-model="editedItem.idconsola"
                  :items="consolas"
                  :hint="`Consola: ${editedItem.idconsola}`"
                  item-title="nombre"
                  item-value="idconsola"
                  label="Consola"
                  persistent-hint
                  single-line
                  ></v-select>
                </v-col>
                <v-col cols="12" md="4" sm="6">
                  <v-select
                    v-model="editedItem.idplataforma"
                    :items="plataformas"
                    :hint="`Plataforma: ${editedItem.idplataforma}`"
                    item-title="nombre"
                    item-value="idplataforma"
                    label="Plataforma"
                    persistent-hint
                    single-line
                  ></v-select>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" md="4" sm="6">
                  <v-text-field
                    type="date"
                    v-model="editedItem.fecha_adquirido_edit"
                    label="Fecha Adquirido"
                  ></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" md="4" sm="6">
                  <v-radio-group inline label="Estado" v-model="editedItem.estado">
                    <v-radio label="Activo" value="Activo" color="success"></v-radio>
                    <v-radio label="En pausa" value="En pausa" color="warning"></v-radio>
                    <v-radio label="Backlog" value="Backlog" color="info"></v-radio>
                    <v-radio label="Completado" value="Completado" color="error"></v-radio>
                  </v-radio-group>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" md="4" sm="6">
                  <v-checkbox
                    v-if="editedItem > -1"
                    v-model="siCompletado"
                    label="Completado"
                  ></v-checkbox>
                  <v-checkbox
                    v-else
                    v-model="editedItem.completado"
                    label="Completado"
                  ></v-checkbox>
                </v-col>
                <v-col cols="12" md="4" sm="6">
                  <v-text-field
                    v-if="editedIndex > -1"
                    type="date"
                    v-model="editedItem.fecha_completado_edit"
                    label="Fecha Completado"
                    :disabled="!editedItem.completado"
                  ></v-text-field>
                  <v-text-field
                    v-else
                    type="date"
                    v-model="editedItem.fecha_completado_edit"
                    label="Fecha Completado"
                    :disabled="!editedItem.completado"
                  ></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" md="4" sm="6">
                  <v-textarea label="Nota" v-model="editedItem.nota"></v-textarea>
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
      <v-dialog
        v-model="dialogSesion" max-width="500px"
      >
        <v-card :title="'Sesión para ' + editedItem.nombre">
          <v-alert
            v-model="alertSesion"
            :type="tipoAlertSesion"
            closable
          >{{ textoAlertSesion }}</v-alert>
          <v-card-text>
            <v-label v-if="enSesion">
                <v-card-text>En Sesión</v-card-text>
            </v-label>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="green-darken-1" variant="text" @click="inicioSesion">Inicio</v-btn>
            <v-btn color="red-darken-1" variant="text" @click="finSesion">Fin</v-btn>
            <v-btn color="blue-darken-1" variant="text" @click="closeSesion">Cerrar</v-btn>
            <v-spacer></v-spacer>
          </v-card-actions>
        </v-card>
    </v-dialog>
    <v-dialog
      v-model="dialogDB"
      max-width="500px"
      max-height="500px"
    >
    <template v-slot:activator="{ props }">
      <v-btn class="mb-2" color="primary" dark v-bind="props">Prueba</v-btn>
    </template>
      <v-card title="Sesión de pruebas">
                  <v-alert
            v-model="alertSesion"
            :type="tipoAlertSesion"
            closable
          >{{ textoAlertSesion }}</v-alert>
        <v-card-text>
          <v-container>
              <v-row v-if="!enSesion">
                <v-col cols="12" md="4" sm="6">
                  <v-radio-group inline label="Tipo" v-model="editedItem.idjuegos">
                    <v-radio label="Demo" :value="0"></v-radio>
                    <v-radio label="Beta" :value="-1"></v-radio>
                  </v-radio-group>
                </v-col>
              </v-row>
              <v-row v-if="!enSesion">
                <v-col cols="12" md="4" sm="6">
                  <v-text-field v-model="editedItem.nombre"
                  label="Nombre"></v-text-field>
                </v-col>
              </v-row>
              <v-row v-if="!enSesion">
                <v-col cols="12" md="4" sm="6">
                  <v-select
                  v-model="editedItem.idconsola"
                  :items="consolas"
                  item-title="nombre"
                  item-value="idconsola"
                  label="Consola"
                  single-line
                  ></v-select>
                </v-col>
                <v-col cols="12" md="4" sm="6">
                  <v-select
                    v-model="editedItem.idplataforma"
                    :items="plataformas"
                    item-title="nombre"
                    item-value="idplataforma"
                    label="Plataforma"
                    single-line
                  ></v-select>
                </v-col>
              </v-row>
          </v-container>
            <v-label v-if="enSesion">
                <v-card-text>En Sesión</v-card-text>
            </v-label>
        </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="green-darken-1" variant="text" @click="inicioSesionDB">Inicio</v-btn>
            <v-btn color="red-darken-1" variant="text" @click="finSesionDB">Fin</v-btn>
            <v-btn color="blue-darken-1" variant="text" @click="closeSesionDB">Cerrar</v-btn>
            <v-spacer></v-spacer>
          </v-card-actions>
      </v-card>
    </v-dialog>
    </v-toolbar>
  </template>

  <template v-slot:item.estado="{ item }">
    <div class="text-end">
      <v-chip
        :color="item.estado === 'Activo' ? 'green' : 'red'"
        :text="item.estado === 'Activo' ? 'Activo' : item.estado"
        class="text-uppercase"
        size="small"
        label
      ></v-chip>
    </div>
  </template>

  <template v-slot:item.actions="{ item }">
    <v-icon
    class="me-2"
    size="small"
    @click="editItem(item)">mdi-pencil</v-icon>
    <v-icon
      class="me-2"
      size="small"
      @click="sesionItem(item)">mdi-play</v-icon>
  </template>
  <template v-slot:no-data>
    <v-btn color="primary" @click="initialize">Reset</v-btn>
  </template>
</v-data-table>
</template>
