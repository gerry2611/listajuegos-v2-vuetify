<script>
/* eslint-disable */
import Plotly from 'plotly.js-dist';

export default{
  props: {
    data: Array,
    layout: Object
  },
  mounted(){this.renderChart()},
  methods: {
    renderChart(){
      Plotly.newPlot(this.$refs.plotElement, this.data, this.layout);
    }
  }
}
</script>

<template>
  Proximamente
  <br>
  <div ref="plotElement"></div>
</template>
