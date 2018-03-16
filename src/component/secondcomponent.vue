<template>
  <svg id="secondcomponent" width="1000" height="600">
     
  </svg>
</template>

<script type="text/javascript">
import * as d3 from "d3";

//定义右转箭头
const PATH_RIGHT =
  "M75 0 L75 95 L30 95 L35 115 L0 82 L35 50 L30 70 L55 70 L55 0 Z";
//定义直行箭头
const PATH_STRAIGHT =
  "M20 0 L20 95 L20 120 L30 120 L10 150 L-10 120 L0 120 L0 0 Z";
//定义左转箭头
const PATH_LEFT =
  "M20 0 L20 70 L45 70 L40 50 L75 82 L40 115 L45 95 L0 95 L0 0 Z";
//左转加直行
const PATH_LEFT_STRAIGHT =
  "M20 0 L20 70 L45 70 L40 50 L75 82 L40 115 L45 95 L20 95 L20 120 L30 120 L10 150 L-10 120 L0 120 L0 0 Z";

//右转加直行
const PATH_RIGHT_STRAIGHT  =
  "M75 0 L75 120 L85 120 L65 150 L45 120 L55 120 L55 95 L30 95 L35 115 L0 82 L35 50 L30 70 L55 70 L55 0 Z";

//右转加直行
const PATH_RIGHT_STRAIGHT_LEFT  =
  "M75 0 L75 70 L100 70 L95 50 L130 82 L95 115 L100 95 L75 95 L75 120 L85 120 L65 150 L45 120 L55 120 L55 95 L30 95 L35 115 L0 82 L35 50 L30 70 L55 70 L55 0 Z";


export default {
  name: "vue-d3-cross",
  methods: {
    renderCrossChart: function() {
      var svg = d3.select("#secondcomponent");

      var div = d3
          .select("#app")
          .append("div")
          .attr("class", "tooltip")
          .style("opacity", 0);
      
      svg.append("path")
        .attr("d",PATH_RIGHT_STRAIGHT)
        .attr("fill","#f89c20")
        .attr("style","transform: translate(400px,200px) rotate(270deg)")
        .on("mouseover", function(d) {
            div
              .transition()
              .duration(200)
              .style("opacity", 0.9);
            div
              .html("流量: " )
              .style("left", d3.event.pageX + "px")
              .style("top", d3.event.pageY - 28 + "px");
          })
          .on("mouseout", function(d) {
            div
              .transition()
              .duration(500)
              .style("opacity", 0);
          });

      svg.append("path")
        .attr("d",PATH_STRAIGHT)
        .attr("fill","#f89c20")
        .attr("transform","translate(188,90)")
      svg.append("path")
        .attr("d",PATH_LEFT_STRAIGHT )
        .attr("fill","#f89c20")
        .attr("transform","translate(230,90)")
      
    }
  },
  mounted: function() {
    this.renderCrossChart();
  },
  data: function() {
    return {};
  }
};
</script>

<style scoped>
.orange {
  fill: #f89c20;
}
.orange-red {
  fill: #d02e27;
}
.red {
  fill: #e54e26;
}
.red-purple {
  fill: #7f1333;
}
.purple {
  fill: #694d9f;
}
.purple-cyan {
  fill: #263c81;
}
.cyan {
  fill: #2eb3c4;
}
.gray {
  fill: #474747;
}
.lightgray {
  fill: #a3a3a3;
}
#secondcomponent {
  margin-left: 50px;
}

</style>