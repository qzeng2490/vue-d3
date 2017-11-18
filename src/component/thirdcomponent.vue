<template>
  <svg id="thirdcomponent" width="600" height="600">
    
  </svg>

</template>

<script>
import * as d3 from 'd3';

export default {
  name: 'vue-d3-pie',
  methods: {
    renderChart: function(filePath1,filePath2) {
      
      var svg = d3.select(this.$el),
        margin = {top: 50, right: 50, bottom: 50, left: 50},
        width = +svg.attr("width") - margin.left - margin.right,
        height = +svg.attr("height") - margin.top - margin.bottom,
        g = svg.append("g").attr("transform", "translate(" + margin.left + "," + margin.top + ")");

    }

  },
  mounted: function() {
    this.renderChart();
  },
}
</script>

<style scoped>


</style>
