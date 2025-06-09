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
    <template v-slot:item.fecha="{ item }">
      <span>{{ new Date(item.fecha).toLocaleDateString() }}</span>
    </template>

    <template v-slot:item.ingreso="{ item }">
      <span v-if="item.ingreso > 0 || item.ingreso != null">
        {{ '$' + item.ingreso }}
      </span>
    </template>

    <template v-slot:item.gasto="{ item }">
      <span v-if="item.gasto > 0 || item.gasto != null">
        {{ '$' + item.gasto }}
      </span>
    </template>

    <template v-slot:item.descuento="{ item }">
      <span v-if="item.descuento > 0 || item.descuento != null">
        {{ item.descuento + '%' }}
      </span>
    </template>

    <template v-slot:item.precio_regular="{ item }">
      <span v-if="item.precio_regular > 0 || item.precio_regular != null">
        {{ '$' + item.precio_regular }}
      </span>
    </template>
  </v-data-table>
</template>
