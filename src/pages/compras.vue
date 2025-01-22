<script>
  /* eslint-disable */
  import axios from 'axios';
  export default{
    data(){
      return{
        compras: {},
        headers: [
          {
            title: 'Fecha',
            align: 'start',
            key: 'fecha'
          },
          { title: 'Tienda', align: 'start', key: 'tienda'},
          { title: 'Ingreso', align: 'center', sortable: false, key: 'ingreso'},
          { title: 'Gasto', align: 'center', sortable: false, key: 'gasto'},
          { title: 'Adquirido', align: 'justify', sortable: false, key: 'adquirido'},
          { title: 'Tipo', align: 'start', sortable: false, key: 'tipo'},
          { title: 'Descuento', align: 'center', sortable: false, key: 'descuento'},
          { title: 'Precio Regular', align: 'center', sortable: false, key: 'precio_regular'},
          { title: 'Nota', align: 'justify', sortable: false, key: 'nota'}
        ],
        alert: false,
        textoAlert: "",
        tipoAlert: ''
      }
    },
    mounted(){this.cargaTablaCompras();},
    methods: {
      cargaTablaCompras: function(){
        axios.get("/api/lista_compras").then((res) => {
          this.compras = res.data.data;
        }).catch(() => {
          this.textoAlert = "Algo anda mal con la tabla 'registro_compras'";
          this.tipoAlert = 'error'
          this.alert = true
        })
      },
      initialize(){
        this.cargaTablaCompras();
      }
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
    :items="compras"
    :sort-by="[{ key: 'movimiento', order: 'asc'}]"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Compras</v-toolbar-title>
      </v-toolbar>
    </template>
  </v-data-table>
</template>
