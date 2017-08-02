---
layout: post
Title: Predicting Predatory Financial Practices at For Profit Colleges
---

<!DOCTYPE html>
<meta charset="utf-8">
<style>

ect {
  fill: transparent;
  shape-rendering: crispEdges;
}

.axis path,
.axis line {
  fill: none;
  stroke: rgba(0, 0, 0, 0.1);
  shape-rendering: crispEdges;
}

.axisLine {
  fill: none;
  shape-rendering: crispEdges;
  stroke: rgba(0, 0, 0, 0.5);
  stroke-width: 2px;
}

.dot {
  fill-opacity: .5;
}

.d3-tip {
  line-height: 1;
  font-weight: bold;
  padding: 12px;
  background: rgba(0, 0, 0, 0.8);
  color: #fff;
  border-radius: 2px;
}

/* Creates a small triangle extender for the tooltip */
.d3-tip:after {
  box-sizing: border-box;
  display: inline;
  font-size: 10px;
  width: 100%;
  line-height: 1;
  color: rgba(0, 0, 0, 0.8);
  content: "\25BC";
  position: absolute;
  text-align: center;
}

/* Style northward tooltips differently */
.d3-tip.n:after {
  margin: -1px 0 0 0;
  top: 100%;
  left: 0;
}

</style>
<body>
<script src="https://d3js.org/d3.v3.min.js"></script>
<script src="https://labratrevenge.com/d3-tip/javascripts/d3.tip.v0.6.3.js"></script>
<script>

var margin = { top: 100, right: 250, bottom: 100, left: 100 },
   outerWidth = 1000,
   outerHeight = 800,
    width = outerWidth - margin.left - margin.right,
    height = outerHeight - margin.top - margin.bottom;


var x = d3.scale.linear()
    .range([0, width]).nice();
var y = d3.scale.linear()
    .range([height, 0]).nice();


var xCat = "tuition revenue per fte";
    yCat = "instructional expenditure per fte";
   //  yCat = "faculty salary";
 //  xCat = "percent fafsa"
  // yCat = "percent pell grant"
  //xCat = "5 year declining balance"
  //yCat = "7 yr repayment completion"
    rCat = "branches";
    myname = "schoolname";
    colorCat = "type";
    mybranches = "branches";
    under_invest = "under investigation";


d3.csv("myschools.csv", function(data) {
  data.forEach(function(d) {
   
   // d["faculty salary"] = +d["faculty salary"]
    
});

  var xMax = d3.max(data, function(d) { return d[xCat]; }) * 1.05,
      xMin = d3.min(data, function(d) { return d[xCat]; }),
      xMin = xMin > 0 ? 0 : xMin,
      yMax = d3.max(data, function(d) { return d[yCat]; }) * 1.05,
      yMin = d3.min(data, function(d) { return d[yCat]; }),
      yMin = yMin > 0 ? 0 : yMin;

  x.domain([0, 20000]);
  y.domain([0, 12000]);
  //x.domain([0, 1]);
  //y.domain([0, 1]);

  var xAxis = d3.svg.axis()
      .scale(x)
      .orient("bottom")
      .tickSize(-height);

  var yAxis = d3.svg.axis()
      .scale(y)
      .orient("left")
      .tickSize(-width)


//  var color = d3.scale.category10();
  var color = d3.scale.ordinal()
  .domain(['non profit', 'for profit', 'non profit under investigation', 'for profit under investigation'])
  .range(["green", "orange" , "blue", 'red']);
//  .domain(['non profit', 'for profit'])
 // .range(["green", "orange" ]);
  var tip = d3.tip()
      .attr("class", "d3-tip")
      .offset([-10, 0])
      .html(function(d) {
       console.log(d);
      //  return xCat + ": " + d[xCat] + "<br>" + yCat + ": " + d[yCat];
     return d[myname] + "<br>" + "Branches: " + d[mybranches] + "<br>" + d[colorCat]; 
     });

  var zoomBeh = d3.behavior.zoom()
      .x(x)
      .y(y)
      .scaleExtent([0, 500])
      .on("zoom", zoom);

  var svg = d3.select("#scatter")
    .append("svg")
      .attr("width", outerWidth)
      .attr("height", outerHeight)
    .append("g")
      .attr("transform", "translate(" + margin.left + "," + margin.top + ")")
      .call(zoomBeh);

  svg.call(tip);

  svg.append("rect")
      .attr("width", width)
      .attr("height", height);

  svg.append("g")
      .classed("x axis", true)
      .attr("transform", "translate(0," + height + ")")
      .call(xAxis)
    .append("text")
      .classed("label", true)
      .attr("x", width)
      .attr("y", margin.bottom -25)
      .style("text-anchor", "end")
      .text(xCat);

  svg.append("g")
      .classed("y axis", true)
      .call(yAxis)
    .append("text")
      .classed("label", true)
      .attr("transform", "rotate(-90)")
      .attr("y", -margin.left)
      .attr("dy", ".71em")
      .style("text-anchor", "end")
      .text(yCat);

  svg.append("g")
  .append("text")
  .classed("label", true)
    .attr("x", width / 2 )
    .attr("y", -10)
    .style("text-anchor", "middle")
    .text("Tuition Revenue vs. Instructional Expenditure");



  var objects = svg.append("svg")
      .classed("objects", true)
      .attr("width", width)
      .attr("height", height);

  objects.append("svg:line")
      .classed("axisLine hAxisLine", true)
      .attr("x1", 0)
      .attr("y1", 0)
      .attr("x2", width)
      .attr("y2", 0)
      .attr("transform", "translate(0," + height + ")");

  objects.append("svg:line")
      .classed("axisLine vAxisLine", true)
      .attr("x1", 0)
      .attr("y1", 0)
      .attr("x2", 0)
      .attr("y2", height);


  objects.selectAll(".dot")
      .data(data)
    .enter().append("circle")
      .classed("dot", true)
      .attr("r", function (d) { return Math.sqrt(d[rCat] / Math.PI; })
     //.attr("r", function (d) { return d[rCat]; })
      .attr("transform", transform)
      .style("fill", function(d) { return color(d[colorCat]); })
     // .attr("r", function (d) { return d[rCat]; })
      //.attr("transform", transform)
     // .style("stroke-width", 5)    // set the stroke width
      //.style("stroke", function(d) { return color(d[colorCat]); })      // set the line colour
      //.style("fill", "none")  
      .on("mouseover", tip.show)
      .on("mouseout", tip.hide);

  var legend = svg.selectAll(".legend")
      .data(color.domain())
    .enter().append("g")
      .classed("legend", true)
      .attr("transform", function(d, i) { return "translate(0," + i * 20 + ")"; });

  legend.append("circle")
      .attr("r", 3.5)
      .attr("cx", width + 20)
      .attr("fill", color);

  legend.append("text")
      .attr("x", width + 26)
      .attr("dy", ".59em")
      .text(function(d) { return d; });

  d3.select("input").on("click", change);

  function zoom() {
    svg.select(".x.axis").call(xAxis);
    svg.select(".y.axis").call(yAxis);

    svg.selectAll(".dot")
        .attr("transform", transform);
  }

  function transform(d) {
    return "translate(" + x(d[xCat]) + "," + y(d[yCat]) + ")";
  }
});

</script>

