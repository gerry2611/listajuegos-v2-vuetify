<script>
/* eslint-disable */
import axios from 'axios';

export default{
  data() {
    return {
      sesiones: {},
      headers: [
        {
          title: 'Sesión No.',
          align: 'start',
          key: 'idsesion'
        },
        {
          title: 'Juego',
          align: 'start',
          key: 'juego'
        },
        { title: 'Consola', align: 'center', key: 'consola'},
        { title: 'Plataforma', align: 'center', key: 'plataforma'},
        { title: 'Inicio', align: 'center', key: 'hora_inicio'},
        { title: 'Fin', align: 'center', key: 'hora_fin'},
        { title: 'Duración', align: 'center', key: 'duracion'},
        { title: 'Demo/Beta', align: 'start', sortable: false, key: 'demo'}
      ],
      alert: false,
      textoAlert: "",
      tipoAlert: '',
      bNombre: "",
      bPlataforma: "",
      busqueda: "",
      filtroDatos: {}
    }
  },
  mounted(){
    this.cargaTablaSesiones();
  },
  methods: {
    cargaTablaSesiones: function(){
      axios.get("/api/sesiones").then((res) => {
        this.sesiones = res.data.data;
      }).catch(() => {
        this.textoAlert = "Algo anda mal con la tabla 'sesiones', revise la conexión a la base de datos";
        this.tipoAlert = 'error'
        this.alert = true;
      })
    }
  },
  computed: {

  }
};
</script>

<template>
  <div>
    <v-alert v-model="alert" :type="tipoAlert">{{ textoAlert }}</v-alert>
  </div>

  <v-data-table
    :headers="headers"
    :items="sesiones"
    :sort-by="[{ key: 'idsesion', order: 'asc' }]"
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Sesiones</v-toolbar-title>
      </v-toolbar>
    </template>
  </v-data-table>
</template>
