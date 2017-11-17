<template>
  <div id="firstcomponent">
    <h1>I am a title.</h1>
    <a> written by {{ author }} </a>
  </div>
</template>

<script type="text/javascript">
export default {
	name: 'vue-d3-radar',
	methods: {
		renderChart: function(filePath){

		}
	},
	mounted: function() {
    this.renderChart("src/assets/radar.csv");
  },
  data: function(){
  	return {
  		author: "Qiang Zeng"
  	}
  }
}
</script>

<style>
</style>
