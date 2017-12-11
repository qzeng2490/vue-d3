<template>
  <svg id="firstcomponent" width="1200" height="600">

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


const flowData = "Time,卡口,视频\n0:00,2704,4499\n1:00,2027,3277\n2:00,1208,2141\n3:00,1140,1938\n4:00,8943,1558\n5:00,4374,1345"

const cycleData = "Time,Cycle\n0:00,125\n1:00,277\n2:00,208\n3:00,558\n4:00,737\n5:00,345";


export default {
	name: 'vue-d3-radar',
	methods: {

	},
	mounted: function() {
    renderBarGroupChart: function() {

      var svg = d3.select("#firstcomponent"),
        margin = {top: 20, right: 50, bottom: 30, left: 40},
        width = +svg.attr("width") - margin.left - margin.right,
        height = +svg.attr("height") - margin.top - margin.bottom,
        g = svg.append("g").attr("transform", "translate(" + margin.left + "," + margin.top + ")");

      var x0 = d3.scaleBand()
          .rangeRound([0, width])
          .paddingInner(0.1);

      var x1 = d3.scaleBand();

      var y = d3.scaleLinear()
          .rangeRound([height, 0]);

      var z = d3.scaleOrdinal()
          .range(["#03a9f4", "#6f0082"]);


      var div = d3.select("#app").append("div")
                  .attr("class", "tooltip")
                  .style("opacity", 0);

      var line = d3.line()
                  .x(function(d) { return x0(d.Time); })
                  .y(function(d) { return y(d.Cycle); });

      var data = d3.csvParse(flowData);
      data.map(function(d,i){
        for (var i = 1, n = data.columns.length; i < n; ++i) d[data.columns[i]] = +d[data.columns[i]];
      })

      var keys = data.columns.slice(1);
      x0.domain(data.map(function(d) { return d.Time; }));
      x1.domain(keys).rangeRound([0, x0.bandwidth()]);

      y.domain([0, d3.max(data, function(d) { return d3.max(keys, function(key) { return d[key]; }); })]).nice();

      g.append("g")
        .selectAll("g")
        .data(data)
        .enter().append("g")
          .attr("transform", function(d) { return "translate(" + x0(d.Time) + ",0)"; })
        .selectAll("rect")
        .data(function(d) { return keys.map(function(key) { return {key: key, value: d[key]}; }); })
        .enter().append("rect")
          .attr("x", function(d) { return x1(d.key); })
          .attr("y", function(d) { return y(d.value); })
          .attr("width", x1.bandwidth())
          .attr("height", function(d) { return height - y(d.value); })
          .attr("fill", function(d) { return z(d.key); })
          .on("mouseover", function(d) {
            div.transition()
              .duration(200)
              .style("opacity", .9);
            div.html("流量: "  + d.value)
              .style("left", (d3.event.pageX) + "px")
              .style("top", (d3.event.pageY - 28) + "px");
          })
          .on("mouseout", function(d) {
              div.transition()
                  .duration(500)
                  .style("opacity", 0);
          });

      g.append("g")
          .attr("class", "axis")
          .attr("transform", "translate(0," + height + ")")
          .call(d3.axisBottom(x0));

      g.append("g")
          .attr("class", "axis")
          .call(d3.axisLeft(y).ticks(null, "s"))
        .append("text")
          .attr("x", 2)
          .attr("y", 0.5)
          .attr("dy", "0.32em")
          .attr("font-weight", "bold")
          .attr("text-anchor", "start")
          .text("车辆数");

      var legend = g.append("g")
          .attr("font-family", "sans-serif")
          .attr("font-size", 10)
          .attr("text-anchor", "end")
        .selectAll("g")
        .data(keys.slice().reverse())
        .enter().append("g")
          .attr("transform", function(d, i) { return "translate(0," + i * 20 + ")"; });

      legend.append("rect")
          .attr("x", width - 60)
          .attr("width", 19)
          .attr("height", 19)
          .attr("fill", z);

      legend.append("text")
          .attr("x", width - 64)
          .attr("y", 9.5)
          .attr("dy", "0.32em")
          .text(function(d) { return d; });

      //添加周期的Legend
      g.append("g").append('path')
        .attr("class","line")
        .attr("d","M"+(width-65)+",55L"+(width-35)+",55")
        .attr("stroke","#000")

      g.append("g")
        .attr("transform", function(d, i) { return "translate(0,60)"; })
        .append("circle")
        .attr("class", "dot")
        .attr("r", 5)
        .attr("cx", width - 50)
        .attr("cy", -5)

      g.append("text")
        .attr("font-family", "sans-serif")
        .attr("font-size", 10)
        .attr("text-anchor", "end")
        .attr("x", width - 65)
        .attr("y", 60)
        .text("周期");

      data = d3.csvParse(cycleData);
      data.map(function(d,i){
        for (var i = 1, n = data.columns.length; i < n; ++i) d[data.columns[i]] = +d[data.columns[i]];
      })

      y.domain([0, d3.max(data, function(d) { return d.Cycle; })]).nice();
      //add Line
      g.append("path")
        .attr("class", "line")
        .attr("d", line(data))
        .attr("transform","translate("+ x1.bandwidth() +",0)");

       // Add the scatterplot
      g.selectAll("dot")
        .data(data)
        .enter().append("circle")
        .attr("class", "dot")
        .attr("r", 5)
        .attr("cx", function(d) { return x0(d.Time); })
        .attr("cy", function(d) { return y(d.Cycle); })
        .attr("transform","translate("+ x1.bandwidth() +",0)")
        .on("mouseover", function(d) {
            div.transition()
                .duration(200)
                .style("opacity", .9);
            div.html( "周期: "  + d.Cycle)
                .style("left", (d3.event.pageX) + "px")
                .style("top", (d3.event.pageY - 28) + "px");
            })
        .on("mouseout", function(d) {
            div.transition()
                .duration(500)
                .style("opacity", 0);
        });

      g.append("g")
          .attr("class", "axis")

          .attr("transform","translate("+ (width) +",0)")
          .call(d3.axisRight(y).ticks(null, "s"))
        .append("text")
          .attr("x", -32)
          .attr("y", 0.5)
          .attr("dy", "0.32em")

          .attr("font-weight", "bold")
          .attr("text-anchor", "start")
          .text("周期(s)");
    },
  },
  data: function(){
  	return {
  	}
  }
}
</script>

<style scoped>

</style>
