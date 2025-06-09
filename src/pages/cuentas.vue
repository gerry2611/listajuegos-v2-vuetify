<script>
/* eslint-disable */
import axios from 'axios'

export default{
  data(){
    return {
      cuentas: {},
      dialog: false,
      dialogSaldo: false,
      saldoElegido: 0.00,
      valorSaldo: 0.00,
      noeditar: false,
      fechaAnterior: false,
      fecha: '',
      saldoCuentaElegida: [],
      headers: [
        { title: 'Cuenta', align: 'start', sortable: false, key: 'cuenta'},
        { title: 'Consolas', align: 'start', sortable: false, key: 'consolas'},
        { title: 'Saldo', align: 'center', sortable: false, key: 'saldo'},
        { title: 'Acciones', sortable: false, key: 'actions'}
      ],
      editedIndex: -1,
      editedItem: {
        cuenta: '',
        consolas: '',
        saldo: 0.00
      },
      defaultItem: {
        cuenta: '',
        consolas: '',
        saldo: 0.00
      },
      saldoItem: {
        cuenta: '',
        consolas: '',
        saldo: 0.00
      },
      alert: false,
      textoAlert: "",
      tipoAlert: '',
    }
  },
  mounted(){this.cargaTablaCuentas()},
  methods: {
    cargaTablaCuentas: function(){
      axios.get("/api/cuentas_online").then((res) => {
        this.cuentas = res.data.data;
        this.listacuenta = res.data.data;
      })
    },
    initialize(){
      axios.get("/api/cuentas_online").then((res) => {
        this.cuentas = res.data.data;
        this.listacuenta = res.data.data;
      })
    },
    editItem(item){
      this.editedIndex = this.cuentas.indexOf(item)
      this.editedItem = {...item}
      this.dialog = true
      this.noeditar = true
    },
    close(){
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = {...this.defaultItem}
        this.editedIndex = -1
      })
    },
    closeVentanaSaldo(){
      this.dialogSaldo = false
      this.saldoCuentaElegida = []
      this.saldoElegido = 0.00
      this.valorSaldo = 0.00
      this.$nextTick(() => {
        this.saldoItem = {...this.defaultItem}
      })
    },
    save(){
      if(this.editedIndex > -1){

      }else{

      }
      this.close()
    },
    agregaSaldoItem(item){
      this.saldoItem = {...item}
      this.dialogSaldo = true
      if(this.saldoItem.cuenta === "Nintendo eShop"){
        this.saldoCuentaElegida = eshop;
      }else if(this.saldoItem.cuenta === "Playstation Store"){
        this.saldoCuentaElegida = ps;
      }else if(this.saldoItem.cuenta === "Steam"){
        this.saldoCuentaElegida = steam;
      }else if(this.saldoItem.cuenta === "Epic Games Store"){
        this.saldoCuentaElegida = epic;
      }else if(this.saldoItem.cuenta === "EA Play"){
        this.saldoCuentaElegida = ea;
      }else if(this.saldoItem.cuenta === "Battle.net"){
        this.saldoCuentaElegida = battle_net;
      }else if(this.saldoItem.cuenta === "iTunes"){
        this.saldoCuentaElegida = itunes;
      }else if(this.saldoItem.cuenta === "Google Play"){
        this.saldoCuentaElegida = google_play;
      }else if(this.saldoItem.cuenta === "Amazon"){
        this.saldoCuentaElegida = amazon;
      }
    },
    agregarSaldo(){
      if(this.saldoElegido === 'cualquier valor'){
        this.saldoElegido = this.valorSaldo
      }

      axios.post("/api/debito_cuentas", {
        cuenta: this.saldoItem.cuenta,
        monto: this.saldoElegido,
        fechahoy: this.fechaAnterior,
        fecha_anterior: this.fecha
      }).then((res) => {
        this.alert = true
        this.textoAlert = "Ingreso registrado con éxito"
        this.tipoAlert = 'success'
        this.cargaTablaCuentas();
      }).catch(() => {
        this.alert = true
        this.textoAlert = "Error en el ingreso. Intentelo de nuevo."
        this.tipoAlert = 'error'
      })
      this.closeVentanaSaldo();
    },
  },
  computed: {
    formTitle(){
      return this.editedIndex === -1 ? 'Nueva cuenta' : 'Editar cuenta'
    }
  },
  watch: {
    dialog(val){
      val || this.close()
    },
  },
  created(){this.initialize()}
}
</script>

<template>
  <div>
    <v-alert
      v-model="alert"
      :type="tipoAlert"
      closable
    >{{ textoAlert }}</v-alert>
  </div>
  <v-data-table
    :headers="headers"
    :items="cuentas"
  >
  <template v-slot:top>
    <v-toolbar flat>
      <v-toolbar-title>Cuentas</v-toolbar-title>
      <v-divider class="mx-4" inset vertical></v-divider>
      <v-spacer></v-spacer>
      <v-dialog v-model="dialog" min-width="500px" max-width="940px">
        <template v-slot:activator="{props}">
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
                  <v-text-field v-model="editedItem.cuenta" label="Nombre de la cuenta" :disabled="noeditar"></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" md="4" sm="6">
                  <v-text-field v-model="editedItem.consolas" label="Consolas asociadas"></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12" md="4" sm="6">
                  <v-text-field v-model="editedItem.saldo" label="Saldo" prefix="$" model-value="0.00" :disabled="noeditar"></v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue-darken-1" variant="text" @click="close">Cancelar</v-btn>
            <v-btn color="blue-darken-1" variant="text" @click="save">Guardar</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <v-dialog
        v-model="dialogSaldo" min-width="500px" max-width="1540px"
      >
      <v-card>
        <v-card-title class="text-h5">Agregar saldo</v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" md="4" sm="6">
<!--                 <v-select
                  v-model="cuentaElegida"
                  :items="cuentas"
                  item-title="cuenta"
                  item-value="cuenta"
                  label="Cuenta"
                >
              <template v-slot:prepend-item>
                <v-list-item
                  title="Seleccione"
                  value='Seleccione'
                  disabled
                ></v-list-item>
              </template>
              </v-select> -->

              {{ saldoItem.cuenta }} - Saldo actual: ${{ saldoItem.saldo }}

              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" md="4" sm="6">
                Saldo
                <br>
                <div v-if="saldoCuentaElegida.length > 0">
                  <!-- <v-btn v-for="monto in eshop" :value="monto">${{ monto }}</v-btn> -->
                   <v-radio-group inline v-model="saldoElegido">
                    <v-radio v-for="monto in saldoCuentaElegida" :label="'$' + monto" :value="monto"></v-radio>
                   </v-radio-group>
                </div>
                <div v-else>
                  Seleccione
                </div>
                <div v-if="saldoElegido === 'cualquier valor' ">
                  <v-text-field
                    v-model="valorSaldo"
                    density="compact"
                    label="Ingrese valor..."
                    type="number"
                    prefix="$"
                  ></v-text-field>
                </div>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" md="4" sm="6">
                <div v-if="saldoElegido === 'cualquier valor'">
                  Monto a agregar: ${{ valorSaldo }}
                </div>
                <div v-else>
                  Monto a agregar: ${{ saldoElegido }}
                </div>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12" md="4" sm="6">
                <v-checkbox
                  v-model="fechaAnterior"
                  label="Fecha Anterior"
                ></v-checkbox>

                <v-text-field
                  type="date"
                  v-model="fecha"
                  label="Fecha"
                  :disabled="!fechaAnterior"
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
          color="blue-darken-1"
          variant="text"
          @click="closeVentanaSaldo"
          >Cancelar</v-btn>
          <v-btn
          color="blue-darken-1"
          variant="text"
          @click="agregarSaldo"
          >Agregar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    </v-toolbar>
  </template>
  <template v-slot:item.saldo="{item}">
    {{ '$' + item.saldo }}
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
    @click="agregaSaldoItem(item)"
    >mdi-cash</v-icon>
  </template>
  </v-data-table>
</template>
