<template>
  <svg id="firstcomponent" width="600" height="600">

  </svg>
</template>

<script type="text/javascript">
import * as d3 from 'd3';

const data =  [
		  [
			{axis:"相位1",value:0.59},
			{axis:"相位2",value:0.56},
			{axis:"相位3",value:0.42},
			{axis:"相位4",value:0.34},
			{axis:"相位5",value:0.48},
			{axis:"相位6",value:0.14},
		  ],[
			{axis:"相位1",value:0.48},
			{axis:"相位2",value:0.41},
			{axis:"相位3",value:0.27},
			{axis:"相位4",value:0.28},
			{axis:"相位5",value:0.46},
			{axis:"相位6",value:0.29},
		  ]
		];

export default {
	name: 'vue-d3-radar',
	methods: {
		renderChart: function(filePath){

			var color   = d3.scaleOrdinal(d3.schemeCategory10);
			var radians = 2 * Math.PI;
			var maxValue = d3.max(data, function(i){return d3.max(i.map(function(o){return o.value;}))}).toFixed(1); 
			var allAxis = data[0].map(function(i){return i.axis});
			var total = allAxis.length;
			var levels = 6;

			console.log(maxValue);

			var svg = d3.select(this.$el),
        margin = {top: 50, right: 50, bottom: 50, left: 50},
        width = +svg.attr("width") - margin.left - margin.right,
        height = +svg.attr("height") - margin.top - margin.bottom,
        g = svg.append("g").attr("transform", "translate(" + margin.left + "," + margin.top + ")");
 
      var radius = Math.min(width/2, height/2);
      var series = 0;

      var LegendOptions = ['相位关键流量比','绿信比'];

      //Circular segments
			for(var j=0; j<levels-1; j++){
			  var levelFactor = radius*((j+1)/levels);
			  console.log(levelFactor);
			  g.selectAll(".levels")
			   .data(allAxis)
			   .enter()
			   .append("svg:line")
			   .attr("x1", function(d, i){return levelFactor*(1-Math.sin(i*radians/total));})
			   .attr("y1", function(d, i){return levelFactor*(1-Math.cos(i*radians/total));})
			   .attr("x2", function(d, i){return levelFactor*(1-Math.sin((i+1)*radians/total));})
			   .attr("y2", function(d, i){return levelFactor*(1-Math.cos((i+1)*radians/total));})
			   .attr("class", "line")
			   .style("stroke", "grey")
			   .style("stroke-opacity", "0.75")
			   .style("stroke-width", "0.7px")
			   .attr("transform", "translate(" + (width/2-levelFactor) + ", " + (height/2-levelFactor) + ")");
			}

			//Text indicating at what % each level is
			for(var j=0; j<levels; j++){
				// 相当于当前层数的半径
			  var levelFactor = radius*((j+1)/levels);
			  g.selectAll(".levels")
			   .data([1]) //dummy data
			   .enter()
			   .append("svg:text")
			   .attr("x", function(d){return levelFactor*(1-Math.sin(0));})
			   .attr("y", function(d){return levelFactor*(1-Math.cos(0));})
			   .attr("class", "legend")
			   .style("font-family", "sans-serif")
			   .style("font-size", "10px")
			   .attr("transform", "translate(" + (width/2-levelFactor) + ", " + (height/2-levelFactor) + ")")
			   .attr("fill", "#737373")
			   .text(d3.format(".0%")((j+1)*maxValue/levels));

			}

			// draw polygon
			data.forEach(function(y, x){
			  var dataValues = [];
			  g.selectAll(".nodes")
					.data(y, function(j, i){
					  dataValues.push([
							width/2*(1-(Math.max(j.value, 0)/maxValue)*Math.sin(i*radians/total)), 
							height/2*(1-(Math.max(j.value, 0)/maxValue)*Math.cos(i*radians/total))
					  ]);
					});
			  dataValues.push(dataValues[0]);

			  g.selectAll(".area")
				 .data([dataValues])
				 .enter()
				 .append("polygon")
				 .attr("class", "radar-chart-serie"+series)
				 .style("stroke-width", "2px")
				 .style("stroke", color(series))
				 .attr("points",function(d) {
						 var str="";
						 for(var pti=0;pti<d.length;pti++){
							 str=str+d[pti][0]+","+d[pti][1]+" ";
						 }
						 return str;
				  })
				 .style("fill", function(j, i){return color(series)})
				 .style("fill-opacity", 0.3)
				 .on('mouseover', function (d){
									var z = "polygon."+d3.select(this).attr("class");
									g.selectAll("polygon")
									 .transition(200)
									 .style("fill-opacity", 0.1); 
									g.selectAll(z)
									 .transition(200)
									 .style("fill-opacity", .8);
								  })
				 .on('mouseout', function(){
									g.selectAll("polygon")
									 .transition(200)
									 .style("fill-opacity", 0.3);
					});

		  	series++;
			});

			//draw tags(相位1， 相位2 。。。)
			var axis = g.selectAll(".axis")
					.data(allAxis)
					.enter()
					.append("g")
					.attr("class", "axis");

			axis.append("line")
				.attr("x1", width/2)
				.attr("y1", height/2)
				.attr("x2", function(d, i){return width/2*(1-Math.sin(i*radians/total));})
				.attr("y2", function(d, i){return height/2*(1-Math.cos(i*radians/total));})
				.attr("class", "line")
				.style("stroke", "grey")
				.style("stroke-width", "1px");

			axis.append("text")
				.attr("class", "legend")
				.text(function(d){return d})
				.style("font-family", "sans-serif")
				.style("font-size", "11px")
				.attr("text-anchor", "middle")
				.attr("dy", "1.5em")
				.attr("transform", function(d, i){return "translate(0, -10)"})
				.attr("x", function(d, i){return width/2*(1-.75*Math.sin(i*radians/total))-60*Math.sin(i*radians/total);})
				.attr("y", function(d, i){return height/2*(1-Math.cos(i*radians/total))-20*Math.cos(i*radians/total);});

			series = 0;
			// 画点

			//Initiate Legend	
			var legend = g.append("g")
				.attr("class", "legend")
				.attr("height", 100)
				.attr("width", 200)
				.attr('transform', 'translate(90,20)');
				//Create colour squares
				legend.selectAll('rect')
				  .data(LegendOptions)
				  .enter()
				  .append("rect")
				  .attr("x", width - 165)
				  .attr("y", function(d, i){ return i * 20;})
				  .attr("width", 10)
				  .attr("height", 10)
				  .style("fill", function(d, i){ return color(i);});
				//Create text next to squares
				legend.selectAll('text')
				  .data(LegendOptions)
				  .enter()
				  .append("text")
				  .attr("x", width- 152)
				  .attr("y", function(d, i){ return i * 20 + 9;})
				  .attr("font-size", "11px")
				  .attr("fill", "#737373")
				  .text(function(d) { return d; })
		}
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
>>> {

}
</style>
