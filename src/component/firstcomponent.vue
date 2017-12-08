<template>
  <svg id="firstcomponent" width="600" height="600">

  </svg>
</template>

<script type="text/javascript">
import * as d3 from 'd3';


export default {
	name: 'vue-d3-radar',
	methods: {
		
	},
	mounted: function() {
    this.renderChart("src/assets/radar.csv");
  },
  data: function(){
  	return {
  	}
  }
}
</script>

<style scoped>

</style>
