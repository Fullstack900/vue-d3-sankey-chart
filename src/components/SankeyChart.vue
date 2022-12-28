<template>
  <p></p>
</template>

<script>
import * as d3 from "d3";
import * as d3Sankey from "d3-sankey";
export default {
  name: "SankeyChart",
};

// set the dimensions and margins of the graph
var margin = { top: 10, right: 10, bottom: 10, left: 10 },
  width = 900 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// format variables
var formatNumber = d3.format(",.0f"), // zero decimal places
  format = function (d) {
    return formatNumber(d);
  };
// ,
// The line below is where we are getting our colors from, this is used for generating colors.
let color = d3.scaleOrdinal([
  "#3484bb",
  "#ff8b25",
  "#40a940",
  "#da3c3d",
  "#9e75c3",
  "#00D7FF",
  "#00D7FF",
  "#9e75c3",
  "#da3c3d",
  "#40a940",
  "#ff8b25",
  "#3484bb",
]);

// append the svg object to the body of the page
var svg = d3
  .select("body")
  .append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
  .style("background-color", "#f5f5f5")
  .style("border", "4px solid black")
  .style("border-radius", "14px")
  .append("g")
  .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

// Set the sankey diagram properties
var sankey = d3Sankey.sankey().nodeWidth(36).nodePadding(40).size([width, height]);

var path = sankey.links();
console.log(path);
// load the data
d3.json("/sankey.json").then(function (sankeydata) {
  console.log("data", sankeydata);
  var graph = sankey(sankeydata);

  // const colors = ['#EB1D36','#FBDF07','#FFB3B3'];

  // console.log("Graph Links: ", graph.links);

  // Set the color gradients of the graph links:

  // let sourceColors = graph.links.map(graphNode => graphNode['source']['bgcolor']);
  // let targetColors = graph.links.map(graphNode => graphNode['target']['bgcolor']);

  // function calculateColorGradient(sourceColor, targetColor){

  //   var srcColor = sourceColor.slice(1);
  //   var trgColor = targetColor.slice(1);

  //   var ratio = 0.5;
  //   var hex = function(x) {
  //       x = x.toString(16);
  //       return (x.length == 1) ? '0' + x : x;
  //   };

  //   var r = Math.ceil(parseInt(srcColor.substring(0,2), 16) * ratio + parseInt(trgColor.substring(0,2), 16) * (1-ratio));
  //   var g = Math.ceil(parseInt(srcColor.substring(2,4), 16) * ratio + parseInt(trgColor.substring(2,4), 16) * (1-ratio));
  //   var b = Math.ceil(parseInt(srcColor.substring(4,6), 16) * ratio + parseInt(trgColor.substring(4,6), 16) * (1-ratio));

  //   var middle = hex(r) + hex(g) + hex(b);
  //   return '#' + middle;
  // }

  // add in the links
  var link = svg
    .append("g")
    .selectAll(".link")
    .data(graph.links)
    .enter()
    .append("path")
    .attr("class", "link")
    .attr("d", d3Sankey.sankeyLinkHorizontal())
    // .attr("stroke", function (d,i) {
    //`#${Math.floor(Math.random()*16777215).toString(16)}`
    // console.log('d',d);
    // console.log('color',Math.trunc(Math.random()*2));
    // return colors[Math.trunc(Math.random()*3)];
    // return `#${Math.floor(Math.random()*16777215).toString(16)}`;
    // })

    .attr("stroke-width", function (d) {
      return d.width;
    });

  // var link = svg
  // .append("g")
  // .selectAll(".link")
  // .data(graph.links)
  // .enter()
  // .append("path")
  // .attr("class", "link")
  // .attr("d", d3Sankey.sankeyLinkHorizontal());

  // var defs = link.append("defs");

  // var gradient = defs.append("linearGradient")
  // .attr("id", "svgGradient")
  // .attr("x1", "0%")
  // .attr("x2", "100%")
  // .attr("y1", "0%")
  // .attr("y2", "100%");

  // gradient.append("stop")
  // .attr("class", "start")
  // .attr("offset", "0%")
  // .attr("stop-color", "red")
  // .attr("stop-opacity", 1);

  // gradient.append("stop")
  // .attr("class", "end")
  // .attr("offset", "100%")
  // .attr("stop-color", "blue")
  // .attr("stop-opacity", 1);

  //  console.log("gradient", gradient);

  // add the link titles
  link.append("title").text(function (d) {
    // console.log("check d", d);
    return d.source.name + " → " + d.target.name + "\n" + format(d.value);
  });

  // add in the nodes
  var node = svg
    .append("g")
    .selectAll(".node")
    .data(graph.nodes)
    .enter()
    .append("g")
    .attr("class", "node");

  // add the rectangles for the nodes
  node
    .append("rect")
    .attr("x", function (d) {
      return d.x0;
    })
    .attr("y", function (d) {
      return d.y0;
    })
    .attr("height", function (d) {
      return d.y1 - d.y0;
    })
    .attr("width", sankey.nodeWidth())
    .style("fill", function (d) {
      return (d.color = color(d.name.replace(/ .*/, "")));
    })
    .style("stroke", function (d) {
      return d3.rgb(d.color).darker(2);
    })
    .append("title")
    .text(function (d) {
      return d.name + "\n" + format(d.value);
    });

  console.log("These are your links", d3.selectAll(".link")["_groups"]);

  // add in the title for the nodes
  node
    .append("text")
    .attr("x", function (d) {
      return d.x0 - 6;
    })
    .attr("y", function (d) {
      return (d.y1 + d.y0) / 2;
    })
    .attr("dy", "0.35em")
    .attr("text-anchor", "end")
    .text(function (d) {
      return d.name;
    })
    .filter(function (d) {
      return d.x0 < width / 2;
    })
    .attr("x", function (d) {
      return d.x1 + 6;
    })
    .attr("text-anchor", "start");
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
body {
  text-align: center;
  margin-top: 60px;
}
.node rect {
  fill-opacity: 0.9;
  shape-rendering: crispEdges;
}

.node text {
  pointer-events: none;
  font-weight: bolder;
}

.link {
  fill: none;
  stroke: rgb(255, 158, 158);
  stroke-opacity: 0.2;
}

.link:hover {
  stroke-opacity: 0.5;
}
</style>
