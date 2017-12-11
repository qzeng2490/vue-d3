<template>
  <svg id="firstcomponent" width="1000" height="600">

  </svg>
</template>

<script type="text/javascript">
import * as d3 from 'd3';
const cycle = 127;
const dist = [0,300,500,850]; // 与第一个路口距离
const greenStart = [0,10,20,30];
const greenReverseStart = [2,8,23,44];
const ot = [0,23,32,26]; // 与上一个路口的相位差
const green = [23,34,44,28];
const greenReverse = [16,32,22,39];

export default {
	name: 'vue-d3-greenWave',
	methods: {
    renderBarGroupChart: function() {

      var svg = d3.select("#firstcomponent"),
        margin = {top: 20, right: 50, bottom: 30, left: 40},
        width = +svg.attr("width") - margin.left - margin.right,
        height = +svg.attr("height") - margin.top - margin.bottom,
        g = svg.append("g").attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      var maxDist = Math.max.apply(null, dist);
      var bandwidth = 10;
      var categories = ["正向绿波","反向绿波"];

      var x = d3.scaleLinear()
          .domain([0, maxDist])
          .range([0, width]);


      var y = d3.scaleLinear()
          .rangeRound([0,height]);

      var red = "red";

      var accOt = [0];
      ot.reduce(function(acc,cur,index){
        accOt.push(acc + cur);
        return acc + cur;
      });

      var maxGreenStart = greenStart.reduce(function(acc,cur,index){
        return Math.max(acc,cur+accOt[index]);
      });

      var maxGreenReverseStart = greenReverseStart.reduce(function(acc,cur,index){
        return Math.max(acc,cur+accOt[index]);
      });
      

      var maxDomainY = Math.max(maxGreenStart,maxGreenReverseStart);

      y.domain([0, cycle + maxDomainY]);

      //正向绿波
      g.selectAll("rect1")
        .data(dist)
        .enter().append("rect")
          .attr("x", function(d) { return d-bandwidth; })
          .attr("y", function(d,i) { return y(maxDomainY-accOt[i]-greenStart[i]); })
          .attr("width", bandwidth)
          .attr("height", function(d) { return y(cycle); })
          .attr("fill", red);

      g.selectAll("rect1")
        .data(dist)
        .enter().append("rect")
          .attr("x", function(d) { return d-bandwidth; })
          .attr("y", function(d,i) { return y(cycle + maxDomainY - green[i]-accOt[i]-greenStart[i]); })
          .attr("width", bandwidth)
          .attr("height", function(d,i) { return y(green[i]); })
          .attr("fill", "#08c308");


      // 反向绿波
      g.selectAll("rect1")
        .data(dist)
        .enter().append("rect")
          .attr("x", function(d) { return d; })
          .attr("y", function(d,i) { return y(maxDomainY-accOt[i]-greenReverseStart[i]); })
          .attr("width", bandwidth)
          .attr("height", function(d) { return y(cycle); })
          .attr("fill", red);    

      g.selectAll("rect1")
        .data(dist)
        .enter().append("rect")
          .attr("x", function(d) { return d; })
          .attr("y", function(d,i) { return y(cycle + maxDomainY - greenReverse[i]-accOt[i]-greenReverseStart[i]); })
          .attr("width", bandwidth)
          .attr("height", function(d,i) { return y(greenReverse[i]); })
          .attr("fill", "#08c308");

      // 坐标轴
      g.append("g")
          .attr("class", "axis")
          .attr("transform", "translate(0," + height + ")")
          .call(d3.axisBottom(x))
          .append("text")
          .attr("x", 900)
          .attr("y", 15)
          .attr("dy", "0.32em")
          .attr("font-weight", "bold")
          .attr("text-anchor", "start")
          .attr("fill","black")
          .text("距离(m)");

      y.rangeRound([height,0]);
      g.append("g")
          .attr("class", "axis")
          .attr("transform", "translate("+(-bandwidth)+",0)")
          .call(d3.axisLeft(y).ticks(null, "s"))
        .append("text")
          .attr("x", 2)
          .attr("y", 0.5)
          .attr("dy", "0.32em")
          .attr("font-weight", "bold")
          .attr("text-anchor", "start")
          .attr("fill","black")
          .text("时间(s)");
    },
	},
	mounted: function() {
    this.renderBarGroupChart();
  },
  data: function(){
  	return {
  	}
  }
}
</script>

<style scoped>

</style>
