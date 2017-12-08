<template>
  <svg id="firstcomponent" width="600" height="600">

  </svg>
</template>

<script type="text/javascript">
import * as d3 from 'd3';
const cycle = 127;
const dist = [0,300,200,350];
const greenStart = [0,10,20,30];
const startReverseGreen = [2,8,23,44];
const ot = [0,23,32,26]; // 与上一个路口的相位差
const green = [23,34,44,28];
const greenReverse = [16,32,22,39];


export default {
	name: 'vue-d3-radar',
	methods: {
		
	},
	mounted: function() {
    
  },
  data: function(){
  	return {
  	}
  }
}
</script>

<style scoped>

</style>
