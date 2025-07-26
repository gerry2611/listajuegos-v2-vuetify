<script>
/* eslint-disable */
import axios from 'axios';
import {ref} from 'vue';

let d = new Date();

export default{
  data(){
    return{
      juegos: {},
      consolas: {},
      plataformas: {},
      dialog: false,
      dialogOtro: false,
      dialogRegistro: false,
      headers: [
        {
          title: 'Nombre',
          align: 'start',
          sortable: false,
          key: 'nombre'
        },
        { title: 'Tipo', align: 'center', key: 'tipo'},
        { title: 'Consola', align: 'center', key: 'consola'},
        { title: 'Tienda', align: 'center', key: 'tienda'},
        { title: 'Fecha de Lanzamiento', align: 'center', key: 'fecha_lan'},
        { title: 'Precio', align: 'center', key: 'precio'},
        { title: 'Precio en Tiendas', align: 'center', key: 'precio_tienda'},
        { title: 'Nota', align: 'start', sortable: false, key: 'nota'},
        { title: 'Acciones', key: 'actions', sortable: false}
      ],
      editedIndex: -1,
      editedItem: {
        id: 0,
        nombre: '',
        tipo: '',
        idconsola: 0,
        tienda: '',
        fecha_lan_edit: null,
        precio: 0.00,
        precio_tienda: 0.00,
        precio_oferta: 0.00,
        descuento: 0,
        precio_regular: 0.00,
        nota: "",
        fecha_comprado: null,
        fecha_reservado: null,
      },
      defaultItem: {
        id: 0,
        nombre: '',
        tipo: '',
        idconsola: 0,
        tienda: '',
        fecha_lan_edit: null,
        precio: 0.00,
        precio_tienda: 0.00,
        precio_oferta: 0.00,
        descuento: 0,
        precio_regular: 0.00,
        nota: "",
        fecha_comprado: null,
        fecha_reservado: null,
        compra_fecha: null,
        reserva_tienda: null,
      },
      alert: false,
      textoAlert: "",
      tipoAlert: '',
      alertCR: false,
      textoAlertCR: "",
      tipoAlertCR: '',
      oferta: false,
      compraCheck: false,
      reservaCheck: false,
      tiendaCheck: false,
      fechaHoy: false,
      tienda_fisica: '',
      tiendas: ['Físico', 'Nintendo eShop', 'Playstation Store', 'Steam', 'Epic Games Store', 'Battle.net', 'App Store', 'Mac App Store', 'Google Play', 'Amazon']
    }
  },
  mounted(){
    this.cargaTablaDeseados();
    this.cargaListadoConsola();
    this.cargaListadoPlataforma();
  },
  methods: {
    cargaTablaDeseados: function(){
      axios.get("/api/lista_deseados").then((res) => {
        this.juegos = res.data.data;
      }).catch(() => {
        this.alert = true
        this.textoAlert = "Algo anda mal con la tabla 'lista_deseados', revise la conexión a la base de datos."
        this.tipoAlert = 'error'
      })
    },
    cargaListadoConsola: function(){
      axios.get("/api/lista_consolas").then((res) => {
        this.consolas = ref(res.data.data[0]);
      }).catch(() => {
        this.alert = true
        this.textoAlert = "Algo anda mal con el procedimiento DD_Consolas"
        this.tipoAlert = 'error'
      })
    },
    cargaListadoPlataforma: function(){
      axios.get("/api/lista_plataformas").then((res) => {
        this.plataformas = ref(res.data.data[0]);
      }).catch(() => {
        this.alert = true
        this.textoAlert = "Algo anda mal con el procedimiento DD_Plataformas"
        this.tipoAlert = 'error'
      })
    },
    initialize(){
      this.cargaTablaDeseados();
      this.cargaListadoConsola();
      this.cargaListadoPlataforma();
    },
    editItem(item){
      this.editedIndex = this.juegos.indexOf(item)
      this.editedItem = {...item}
      this.dialog = true
    },
    compraItem(item){
      this.editedIndex = this.juegos.indexOf(item)
      this.editedItem = {...item}
      this.dialogOtro = true
    },
    close(){
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = {...this.defaultItem}
        this.editedIndex = -1
      })
    },
    closeCompra(){
      this.dialogOtro = false
      this.dialogRegistro = false
      this.$nextTick(() => {
        this.editedItem = {...this.defaultItem}
        this.editedIndex = -1
        this.compraCheck = false
        this.reservaCheck = false
        this.tiendaCheck = false
        this.fechaHoy = false
        this.oferta = false

      })
    },
    save(){
      if(this.editedIndex > -1){
        axios.post("/api/editar_juego_deseado", {
          idjuego: this.editedItem.id,
          nombre: this.editedItem.nombre,
          tipo: this.editedItem.tipo,
          idconsola: this.editedItem.idconsola,
          tienda: this.editedItem.tienda,
          fecha_lan: this.editedItem.fecha_lan_edit,
          precio: this.editedItem.precio,
          nota: this.editedItem.nota
        }).then((res) => {
          this.alert = true
          this.textoAlert = "Juego editado con éxito"
          this.tipoAlert = 'success'
          this.cargaTablaDeseados();
        }).catch(() => {
          this.alert = true
          this.textoAlert = "Error en la edición"
          this.tipoAlert = 'error'
        })
      }else{
        axios.post("/api/agregar_juego_deseado", {
          nombre: this.editedItem.nombre,
          tipo: this.editedItem.tipo,
          idconsola: this.editedItem.idconsola,
          tienda: this.editedItem.tienda,
          fecha_lanzamiento: this.editedItem.fecha_lan_edit,
          precio: this.editedItem.precio,
          nota: this.editedItem.nota
        }).then((res) => {
          this.alert = true
          this.textoAlert = "Juego guardado con éxito"
          this.tipoAlert = 'success'
          this.cargaTablaDeseados();
        }).catch(() => {
          this.alert = true
          this.textoAlert = "Error al agregar el juego"
          this.tipoAlert = 'error'
        })
      }
      this.close()
    },
    compra(){
      if(this.editedIndex > -1){
        if(this.reservaCheck){
          axios.post("/api/registrar_reserva", {
            idjuego: this.editedItem.id,
            nombre: this.editedItem.nombre,
            tipo: this.editedItem.tipo,
            consola: this.editedItem.idconsola,
            tienda: this.editedItem.tienda,
            reservatienda: this.reservaCheck,
            precio: this.editedItem.precio,
            nota: this.editedItem.nota
          }).then((res) => {
          this.alert = true
          this.textoAlert = "Juego reservado: " + this.editedItem.nombre + "\nDisponible en la lista de reserva"
          this.tipoAlert = 'success'
          this.cargaTablaDeseados();
        }).catch(() => {
          this.alert = true
          this.textoAlert = "Error al registrar la reserva del juego"
          this.tipoAlert = 'error'
        })
        }else if(this.compraCheck && this.oferta){
          axios.post("/api/registrar_compra", {
            idjuego: this.editedItem.id,
            fecha: this.editedItem.fecha_comprado,
            fecha_hoy: this.fechaHoy,
            nombre: this.editedItem.nombre,
            consola: this.editedItem.idconsola,
            tienda: this.editedItem.tienda,
            compratienda: this.tiendaCheck,
            tipo: this.editedItem.tipo,
            precio: this.editedItem.precio_oferta,
            precio_oferta: this.oferta,
            descuento: this.editedItem.descuento,
            precio_regular: this.editedItem.precio,
            nota: this.editedItem.nota
          }).then((res) => {
          this.alert = true
          this.textoAlert = "Juego comprado: " + this.editedItem.nombre + "\nDisponible en el backlog"
          this.tipoAlert = 'success'
          this.cargaTablaDeseados();
        }).catch(() => {
          this.alert = true
          this.textoAlert = "Error al registrar la compra del juego"
          this.tipoAlert = 'error'
        })
        }
      }
      this.closeCompra()
    },
    registro(){
      if(this.reservaCheck){
          axios.post("/api/registrar_reserva_fecha", {
            fecha: this.editedItem.fecha_reservado,
            nombre: this.editedItem.nombre,
            tipo: this.editedItem.tipo,
            consola: this.editedItem.idconsola,
            tienda: this.editedItem.tienda,
            reservatienda: this.reservaCheck,
            precio: this.editedItem.precio,
            nota: this.editedItem.nota
          }).then((res) => {
          this.alert = true
          this.textoAlert = "Juego reservado: " + this.editedItem.nombre + "\nDisponible en la lista de reserva"
          this.tipoAlert = 'success'
          this.cargaTablaDeseados();
        }).catch(() => {
          this.alert = true
          this.textoAlert = "Error al registrar la reserva del juego"
          this.tipoAlert = 'error'
        })
        }else if(this.compraCheck && this.oferta && this.tiendaCheck){
          axios.post("/api/registrar_compra_fecha", {
            fecha: this.editedItem.fecha_comprado,
            nombre: this.editedItem.nombre,
            tipo: this.editedItem.tipo,
            consola: this.editedItem.idconsola,
            tienda: this.tienda_fisica,
            tienda_fisico: this.tiendaCheck,
            precio: this.editedItem.precio_oferta,
            precio_oferta: this.oferta,
            descuento: this.editedItem.descuento,
            precio_regular: this.editedItem.precio,
            nota: this.editedItem.nota
          }).then((res) => {
          this.alert = true
          this.textoAlert = "Juego comprado: " + this.editedItem.nombre + "\nDisponible en el backlog"
          this.tipoAlert = 'success'
          this.cargaTablaDeseados();
        }).catch(() => {
          this.alert = true
          this.textoAlert = "Error al registrar la compra del juego"
          this.tipoAlert = 'error'
        })
        }else if(this.compraCheck && this.oferta){
          axios.post("/api/registrar_compra_fecha", {
            fecha: this.editedItem.fecha_comprado,
            nombre: this.editedItem.nombre,
            tipo: this.editedItem.tipo,
            consola: this.editedItem.idconsola,
            tienda: this.editedItem.tienda,
            precio: this.editedItem.precio_oferta,
            precio_oferta: this.oferta,
            descuento: this.editedItem.descuento,
            precio_regular: this.editedItem.precio,
            nota: this.editedItem.nota
          }).then((res) => {
          this.alert = true
          this.textoAlert = "Juego comprado: " + this.editedItem.nombre + "\nDisponible en el backlog"
          this.tipoAlert = 'success'
          this.cargaTablaDeseados();
        }).catch(() => {
          this.alert = true
          this.textoAlert = "Error al registrar la compra del juego"
          this.tipoAlert = 'error'
        })
        }else{
          axios.post("/api/registrar_compra_fecha", {
            fecha: this.editedItem.fecha_comprado,
            nombre: this.editedItem.nombre,
            tipo: this.editedItem.tipo,
            consola: this.editedItem.idconsola,
            tienda: this.editedItem.tienda,
            precio: this.editedItem.precio,
            precio_oferta: this.oferta,
            nota: this.editedItem.nota
          }).then((res) => {
          this.alert = true
          this.textoAlert = "Juego comprado: " + this.editedItem.nombre + "\nDisponible en el backlog"
          this.tipoAlert = 'success'
          this.cargaTablaDeseados();
        }).catch(() => {
          this.alert = true
          this.textoAlert = "Error al registrar la compra del juego"
          this.tipoAlert = 'error'
        })
        }
        this.closeCompra();
    },
    obtenerFechaHoy(){
      if(this.fechaHoy){
        if(this.compraCheck){
          this.editedItem.fecha_comprado = d.toISOString().split('T')[0]
        }else{
          this.editedItem.fecha_reservado = d.toISOString().split('T')[0]
        }
      }
    }
  },
  computed: {
    formTitle(){
      return this.editedIndex === -1 ? 'Nuevo' : 'Editar'
    }
  },
  watch: {
    dialog(val){
      val || this.close()
    },
    dialogOtro(val){
      val || this.close()
    },
  },
  created(){this.initialize()}
}
</script>

<template>
    <div class="d-flex align-center">
      <v-alert
        v-model="alert"
        :type="tipoAlert"
      closable>{{ textoAlert }}</v-alert>
    </div>

    <v-data-table
    :headers="headers"
    :items="juegos"
    :sort-by="[{ key: 'id', order: 'asc' }]"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>Wishlist</v-toolbar-title>
        <v-divider class="mx-4" inset vertical></v-divider>
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
                    label="Nombre"
                    :rules="[() => !!editedItem.nombre || 'Campo requerido']"
                    required
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-radio-group inline label="Tipo" v-model="editedItem.tipo">
                      <v-radio label="Juego" value="Juego"></v-radio>
                      <v-radio label="Complemento" value="Complemento"></v-radio>
                      <v-radio label="Paquete" value="Paquete"></v-radio>
                      <v-radio label="Pase de batalla" value="Pase de batalla"></v-radio>
                      <v-radio label="Otro" value="Otro"></v-radio>
                    </v-radio-group>
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
                      label="Tiendas"
                      v-model="editedItem.tienda"
                      :items="tiendas"
                      :hint="`${editedItem.tienda}`"
                      persistent-hint
                    ></v-select>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      type="date"
                      v-model="editedItem.fecha_lan_edit"
                      label="Fecha de Lanzamiento"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      type="number"
                      v-model="editedItem.precio"
                      label="Precio"
                      prefix="$"
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
              @click="close"
              >Cancelar</v-btn>
              <v-btn
              color="blue-darken-1"
              variant="text"
              @click="save"
              >Guardar</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog
          v-model="dialogOtro" min-width="1000px" max-width="1540px"
        >
        <v-card>
          <v-card-title class="text-h5">Registro de la compra/reserva</v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field v-model="editedItem.nombre"
                    label="Nombre"
                    :rules="[() => !!editedItem.nombre || 'Campo requerido']"
                    required
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-radio-group inline label="Tipo" v-model="editedItem.tipo">
                      <v-radio label="Juego" value="Juego"></v-radio>
                      <v-radio label="Complemento" value="Complemento"></v-radio>
                      <v-radio label="Paquete" value="Paquete"></v-radio>
                      <v-radio label="Pase de batalla" value="Pase de batalla"></v-radio>
                      <v-radio label="Otro" value="Otro"></v-radio>
                    </v-radio-group>
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
                      label="Tiendas"
                      v-model="editedItem.tienda"
                      :items="['Nintendo eShop', 'Playstation Store', 'Steam', 'Epic Games Store', 'App Store', 'Mac App Store']"
                      :hint="`${editedItem.tienda}`"
                      persistent-hint
                      :disabled="tiendaCheck"
                    ></v-select>
                    <v-checkbox v-model="tiendaCheck" label="Compra/Reserva en Tienda Física"></v-checkbox>
                    <div v-if="tiendaCheck">
                      <v-text-field v-model="tienda_fisica" label="Tienda en Físico"></v-text-field>
                    </div>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      type="date"
                      v-model="editedItem.fecha_lan_edit"
                      label="Fecha de Lanzamiento"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      type="number"
                      v-model="editedItem.precio"
                      label="Precio"
                      prefix="$"
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-textarea label="Nota" v-model="editedItem.nota"></v-textarea>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-checkbox
                      v-model="compraCheck"
                      label="Compra"
                      :disabled="reservaCheck"
                    ></v-checkbox>
                    <v-checkbox
                      v-model="reservaCheck"
                      label="Reserva"
                      :disabled="compraCheck"
                    ></v-checkbox>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <div v-if="compraCheck">
                      <v-checkbox
                        v-model="oferta"
                        label="Oferta"
                      ></v-checkbox>
                    </div>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <div v-if="oferta">
                      <v-text-field v-model="editedItem.descuento"
                      label="Descuento"></v-text-field>
                      <v-text-field v-model="editedItem.precio_oferta"
                      label="Precio en Oferta"></v-text-field>
                    </div>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <div v-if="compraCheck">
                      <v-checkbox
                        v-model="fechaHoy"
                        label="Comprado Hoy"
                        @change="obtenerFechaHoy"
                      ></v-checkbox>

                      <v-text-field
                      type="date"
                      v-model="editedItem.fecha_comprado"
                      label="Fecha de la compra"
                      :disabled="fechaHoy"
                    ></v-text-field>
                    </div>
                    <div v-else-if="reservaCheck">
                      <v-checkbox
                        v-model="fechaHoy"
                        label="Reservado Hoy"
                        @change="obtenerFechaHoy"
                      ></v-checkbox>
                      <v-text-field
                      type="date"
                      v-model="editedItem.fecha_reservado"
                      label="Fecha de la reserva"
                      :disabled="fechaHoy"
                    ></v-text-field>
                    </div>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
              color="blue-darken-1"
              variant="text"
              @click="closeCompra"
              >Cancelar</v-btn>
              <v-btn
              color="blue-darken-1"
              variant="text"
              @click="compra"
              >Guardar</v-btn>
            </v-card-actions>
        </v-card>
      </v-dialog>
      <v-dialog v-model="dialogRegistro" min-width="1000px" max-width="1540px">
        <template v-slot:activator="{ props }">
            <v-btn class="mb-2" color="primary" dark v-bind="props">Registro</v-btn>
        </template>
        <v-card>
          <v-card-title class="text-h5">Registro de la compra sin agregar a Wishlist</v-card-title>
            <v-card-text>
              <v-container>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field v-model="editedItem.nombre"
                    label="Nombre"
                    :rules="[() => !!editedItem.nombre || 'Campo requerido']"
                    required
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-radio-group inline label="Tipo" v-model="editedItem.tipo">
                      <v-radio label="Juego" value="Juego"></v-radio>
                      <v-radio label="Complemento" value="Complemento"></v-radio>
                      <v-radio label="Paquete" value="Paquete"></v-radio>
                      <v-radio label="Pase de batalla" value="Pase de batalla"></v-radio>
                      <v-radio label="Otro" value="Otro"></v-radio>
                    </v-radio-group>
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
                      label="Tiendas"
                      v-model="editedItem.tienda"
                      :items="tiendas"
                      :hint="`${editedItem.tienda}`"
                      persistent-hint
                      :disabled="tiendaCheck"
                    ></v-select>
                    <v-checkbox v-model="tiendaCheck" label="Compra/Reserva en Tienda Física"></v-checkbox>
                    <div v-if="tiendaCheck">
                      <v-text-field v-model="tienda_fisica" label="Tienda en Físico"></v-text-field>
                    </div>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      type="date"
                      v-model="editedItem.fecha_lan_edit"
                      label="Fecha de Lanzamiento"
                    ></v-text-field>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <v-text-field
                      type="number"
                      v-model="editedItem.precio"
                      label="Precio"
                      prefix="$"
                    ></v-text-field>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-textarea label="Nota" v-model="editedItem.nota"></v-textarea>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <v-checkbox
                      v-model="compraCheck"
                      label="Compra"
                      :disabled="reservaCheck"
                    ></v-checkbox>
                    <v-checkbox
                      v-model="reservaCheck"
                      label="Reserva"
                      :disabled="compraCheck"
                    ></v-checkbox>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="12" md="4" sm="6">
                    <div v-if="compraCheck">
                      <v-checkbox
                        v-model="oferta"
                        label="Oferta"
                      ></v-checkbox>
                    </div>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <div v-if="oferta">
                      <v-text-field v-model="editedItem.descuento"
                      label="Descuento"></v-text-field>
                      <v-text-field v-model="editedItem.precio_oferta"
                      label="Precio en Oferta"></v-text-field>
                    </div>
                  </v-col>
                  <v-col cols="12" md="4" sm="6">
                    <div v-if="compraCheck">
                      <v-checkbox
                        v-model="fechaHoy"
                        label="Comprado Hoy"
                        @change="obtenerFechaHoy"
                      ></v-checkbox>

                      <v-text-field
                      type="date"
                      v-model="editedItem.fecha_comprado"
                      label="Fecha de la compra"
                      :disabled="fechaHoy"
                    ></v-text-field>
                    </div>
                    <div v-else-if="reservaCheck">
                      <v-checkbox
                        v-model="fechaHoy"
                        label="Reservado Hoy"
                        @change="obtenerFechaHoy"
                      ></v-checkbox>
                      <v-text-field
                      type="date"
                      v-model="editedItem.fecha_reservado"
                      label="Fecha de la reserva"
                      :disabled="fechaHoy"
                    ></v-text-field>
                    </div>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
              color="blue-darken-1"
              variant="text"
              @click="closeCompra"
              >Cancelar</v-btn>
              <v-btn
              color="blue-darken-1"
              variant="text"
              @click="registro"
              >Guardar</v-btn>
            </v-card-actions>
        </v-card>
      </v-dialog>
        </v-toolbar>

      </template>
      <template v-slot:item.precio="{item}">
        <span v-if="item.precio > 0 || item.precio != null">
          {{ '$' + item.precio }}
        </span>
      </template>
      <template v-slot:item.precio_tienda="{item}">
        <span v-if="item.precio_tienda > 0 || item.precio_tienda != null">
          {{ '$' + item.precio_tienda }}
        </span>
      </template>
      <template v-slot:item.actions="{item}">
          <v-icon
            class="me-2"
            size="small"
            @click="editItem(item)"
          >mdi-pencil</v-icon>
          <v-icon
            class="me-2"
            size="small"
            @click="compraItem(item)"
          >mdi-currency-usd</v-icon>
         <!--v-icon registrar reserva-->
      </template>
    </v-data-table>
</template>
