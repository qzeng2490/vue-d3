<template>
  <svg id="secondcomponent" width="960" height="500">
    
  </svg>

  
</template>

<script>
import * as d3 from 'd3';
const cycleData = ""
export default {
  name: 'vue-d3-test',
  methods: {
    renderChart: function(filePath1,filePath2) {
      
      var svg = d3.select(this.$el),
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
          .range(["#98abc5", "#8a89a6"]);

      var div = d3.select("body").append("div") 
                  .attr("class", "tooltip")       
                  .style("opacity", 0);

      var line = d3.line()
                  .x(function(d) { return x0(d.Time); })
                  .y(function(d) { return y(d.Cycle); });

      d3.csv(filePath1, function(d, i, columns) {
        for (var i = 1, n = columns.length; i < n; ++i) d[columns[i]] = +d[columns[i]];
        return d;
      }, function(error, data) {
        if (error) throw error;

        console.log(data);

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
            .attr("fill", "#000")
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
      });

      d3.csv(filePath2, function(d, i, columns) {
        for (var i = 1, n = columns.length; i < n; ++i) d[columns[i]] = +d[columns[i]];
        return d;
      }, function(error, data) {
        if (error) throw error;

        y.domain([0, d3.max(data, function(d) { return d.Cycle; })]).nice();
        console.log(data);
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
            .attr("fill", "#000")
            .attr("font-weight", "bold")
            .attr("text-anchor", "start")
            .text("周期(s)");
      });
    }

  },
  mounted: function() {
    this.renderChart("src/assets/data.csv","src/assets/cycle.csv");
  },
}
</script>

<style scoped>
.axis >>> .domain {
  display: none;
}

>>> div.tooltip { 
    position: absolute;     
    text-align: center;     
    width: 60px;          
    height: 28px;         
    padding: 2px;       
    font: 12px sans-serif;    
    background: lightsteelblue; 
    border: 0px;    
    border-radius: 8px;     
    pointer-events: none;     
}

>>> .line {
  fill: none;
  stroke: steelblue;
  stroke-width: 1.5px;
}

>>> .dot {
  fill: white;
  stroke: steelblue;
  stroke-width: 1.5px;
}

</style>

      // var width = 420,
      //     barHeight = 20;
      // var x = d3.scaleLinear()
      //     .domain([0, d3.max(data)])
      //     .range([0, width]);
      // var chart = d3.select(this.$el)
      //     .attr("width", width)
      //     .attr("height", barHeight * data.length);
      // var d = chart.selectAll("g")
      //     .data(data);
      // d.exit().remove();
      // var g = d.enter().append("g")
      //     .attr("transform", function(d, i) { return "translate(0," + i * barHeight + ")"; });
      // g.selectAll("rect").remove();
      // g.selectAll("text").remove();
      // g.append("rect")
      //     .attr("width", x)
      //     .attr("height", barHeight - 1);
      // g.append("text")
      //     .attr("x", function(d) { return x(d) - 3; })
      //     .attr("y", barHeight / 2)
      //     .attr("dy", ".35em")
      //     .text(function(d) { return d; });