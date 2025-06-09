<script>
/* eslint-disable */
import PlotlyChart from '@/components/PlotlyChart.vue';
import PlotlyChartPie from '@/components/PlotlyChartPie.vue';
import axios from 'axios';
export default{
  components: {
    PlotlyChart
  },
  data(){
    return{
      chartData: [{
        type: "pie",
        values: [],
        labels: [],

        textposition: "outside",
        automargin: true
      }],
      chartLayout: [{
        title: 'Chart de prueba',
        height: 400,
        width: 400,
        margin: {"t": 0, "b": 0, "l": 0, "r": 0},
        showlegend: true,
        textinfo: 'value'
      }],
      tab: 'Juegos por Consola',
      pes: [
        'Juegos por Consola',
        'Juegos por Plataforma'
      ],
      esttab1: {},
      esttab2: {},
      nombres: [],
      valores: [],
      datos: [],
      estplat: {}
    }
  },
  mounted(){
    this.cargaESTJConsola();
    this.cargaESTJPlataforma();
  },
  methods: {
    cargaESTJConsola: function(){
      axios.get("/api/est_juegosxconsola").then((res) => {
        this.esttab1 = res.data.data;
        for(let i = 0; i < this.esttab1.length; i++){
          this.valores[i] = this.esttab1[i].nojuegos;
          this.nombres[i] = this.esttab1[i].nombre;
        }

        this.datos = [{
          type: 'pie',
          values: this.valores,
          labels: this.nombres
        }]


      }).catch(() => {
        console.log("Algo anda mal con estc")
      })
    },
    cargaESTJPlataforma: function(){
      axios.get("/api/est_juegosxplataforma").then((res) => {
        this.esttab2 = res.data.data;
        let datosp = []
        let nombrep = []

        for(let i = 0; i < this.esttab2.length; i++){
          datosp[i] = this.esttab2[i].nojuegos;
          nombrep[i] = this.esttab2[i].nombre;
        }

        this.estplat = [{
          type: 'pie',
          values: datosp,
          labels: nombrep
        }]
      }).catch(() => {
        console.log("Algo anda mal con estp")
      })
    }
  }
}
</script>

<template>

  <v-tabs
    v-model="tab"
    grow
  >
    <v-tab
      v-for="p in pes"
      :key="p"
      :text="p"
      :value="p"
    ></v-tab>
  </v-tabs>

  <v-tabs-window v-model="tab">
    <v-tabs-window-item value="Juegos por Consola">
      <div id="app" style="position:relative;">
        <PlotlyChart :data="datos" :layout="chartLayout" />
      </div>
    </v-tabs-window-item>
    <v-tabs-window-item value="Juegos por Plataforma">
      <div id="app2" style="position: relative;">
        <PlotlyChartPie :data="estplat" :layout="chartLayout" />
      </div>
    </v-tabs-window-item>
  </v-tabs-window>

</template>
