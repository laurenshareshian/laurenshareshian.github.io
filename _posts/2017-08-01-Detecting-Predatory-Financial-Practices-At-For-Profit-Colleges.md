---
layout: post
Title: Predicting Predatory Financial Practices at For Profit Colleges
---

There has been much talk over how much debt students are saddled with as they graduate from college. The most aggregious cases involving for-profit colleges such as University of Phoenix, DeVry, ITT, and Everest College have been well-documented, including on [John Oliver](https://www.youtube.com/watch?v=P8pjd1QEA0c) and in [government reports](https://www.ed.gov/news/press-releases/education-department-releases-final-debt-earnings-rates-gainful-employment-programs). However, even programs as highly regarded as Harvard's drama program and Johns Hopkin's music program have received failing Debt-To-Earning ratings by the government, as chronicled by the [New York Times](https://www.nytimes.com/2017/01/13/upshot/harvard-too-obamas-final-push-to-catch-predatory-colleges-is-revealing.html).

In my data science immersion program, I was assigned the task of using the classifier algorithms that we had learned to predict a topic of interest to me. I chose to predict whether a college would receive a passing or failing score on the [government's student debt analysis](https://studentaid.ed.gov/sa/about/data-center/school/ge). That way, as new college programs pop up each year, prospective students know which schools to be wary of and the government knows which schools to keep its eye on for regulatory issues.

My first step in performing the analysis was to obtain data on colleges. Luckily, there is a wealth of information to be pulled from the [government College Scorecard API](https://collegescorecard.ed.gov/data/documentation/). I pulled all 6,740 schools listed as "in operation" from the US. This included everything from private non-profit schools such as Harvard to large public universities to community colleges to for-profit private schools such as University of Phoenix to small for-profit trade schools with fun names like "Bos-Man's Barber College". It contained a massive amount of features related to student population, curriculum, and financial aid.


My second step was using Python pandas to merge this data set with the one containing the government's [school debt-to-earning ratings](https://studentaid.ed.gov/sa/about/data-center/school/ge). This data set was incomplete in the sense that not every school was listed. In addition, most schools only had a few majors listed. I classifed a school as having a failing debt-to-earnings score if at least one of their majors had a failing score. This was a pretty harsh classifier, as it meant that Harvard and Johns Hopkins failed. A snippet of the government's data set looked like this:


<img src="/images/harvard.png" width="600"/> 


Despite the fact that Harvard and Hopkins made the failing list, only 9% of the programs on the DTE list had a failing status. While 66% of the schools on the list were for-profit, a whopping 97% of the schools listed as failing were for-profit. 

My third step was using the sklearn.feature_selection.SelectKBest method to select features that had a p-value of less than 0.05. Since I wanted to use these features to predict debt-to-earning scores, I highlighted the features related to debt in red and earnings in green. Notice that for-profit status also plays a major feature role. I have crossed the green features out because I will not know a student's repayment status at the time of prediction. Thus, I will delete these variables from my features in order to eliminate information leak:


<img src="/images/features.png" width="600"/> 



How well did these features do in predicting a failing DTE score? Fabulously! Here are the ROC AUCs for the various estimators I implemented:


<img src="/images/ROC.png" width="600"/> 


Using a five-fold test/train split, the XGBoost, for instance, had an average ROC AUC of 97.5, test accuracy of 96.8, precision of 92.3, and recall of 79.2. In plain english, of all the failing DTE schools, I find 79.2% of them. In addition, of all the schools I label as failing, I am correct 92.3% of the time. Not too shabby! While XGBoost performed a hair better than the MLP neural network classifier, it was fun to start playing around with neural networks, too.

Okay, now that we know that it's easy to predict failing schools, let's explore their commonalities. While 43% of the schools in our dataset were for-profit, an overwhelming 97% of the schools classifed with a failing DTE were for-profit. What makes for-profit schools so different? Well, it turns out many things. 

First view the plot below of tuition revenue per fte versus instructional expenditure per fte. Loosely, you can think of this as a comparision of how much the school is profiting from you taking a class versus how much of that expense it is putting back into the classroom. We can see from the D3 graph below that for-profit schools (and especially the failing ones) are taking massive amounts of tuition from students and dedicating very little of it to the curriculum. 

You can move your mouse over to view school names. The radius of the dots correspond to the number of branches. Large chains like DeVry and National American University in the bottom right were among the worst:



<meta charset="utf-8">
<style>

rect {
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



<div id="scatter1"></div>
<script src="https://d3js.org/d3.v3.min.js"></script>
<script src="https://labratrevenge.com/d3-tip/javascripts/d3.tip.v0.6.3.js"></script>
<script>

var margin = { top: 100, right: 250, bottom: 100, left: 100 },
   outerWidth = 900,
   outerHeight = 700,
    width = outerWidth - margin.left - margin.right,
    height = outerHeight - margin.top - margin.bottom;


var x = d3.scale.linear()
    .range([0, width]).nice();
var y = d3.scale.linear()
    .range([height, 0]).nice();


var xCat1 = "tuition revenue per fte";
    yCat1 = "instructional expenditure per fte";
    rCat = "branches";
    myname = "schoolname";
    colorCat = "type";
    mybranches = "branches";
    under_invest = "under investigation";


d3.csv("/myschools.csv", function(data) {
  data.forEach(function(d) {
   
   // d["faculty salary"] = +d["faculty salary"]
    
});

  var xMax1 = d3.max(data, function(d) { return d[xCat1]; }) * 1.05,
      xMin1 = d3.min(data, function(d) { return d[xCat1]; }),
      xMin1 = xMin1 > 0 ? 0 : xMin1,
      yMax1 = d3.max(data, function(d) { return d[yCat1]; }) * 1.05,
      yMin1 = d3.min(data, function(d) { return d[yCat1]; }),
      yMin1 = yMin1 > 0 ? 0 : yMin1;

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

  var svg = d3.select("#scatter1")
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
      .text(xCat1);

  svg.append("g")
      .classed("y axis", true)
      .call(yAxis)
    .append("text")
      .classed("label", true)
      .attr("transform", "rotate(-90)")
      .attr("y", -margin.left)
      .attr("dy", ".71em")
      .style("text-anchor", "end")
      .text(yCat1);

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
      .attr("r", function (d) { return 6*Math.sqrt(d[rCat] / Math.PI); })
      .attr("transform", transform)
      .style("fill", function(d) { return color(d[colorCat]); })
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
    return "translate(" + x(d[xCat1]) + "," + y(d[yCat1]) + ")";
  }
});

</script>


In addition, we can see from the plot below that while for-profit schools are taking in a lot of tuition from students, very little of that tuition is going towards faculty salaries. Faculty salaries at for-profits are considerably lower. You can see that the University of Phoenix has one of the worst faculty salaries:




<meta charset="utf-8">
<style>

rect {
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



<div id="scatter2"></div>
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


var xCat2 = "tuition revenue per fte";
     yCat2 = "faculty salary";
    rCat = "branches";
    myname = "schoolname";
    colorCat = "type";
    mybranches = "branches";
    under_invest = "under investigation";


d3.csv("/myschools.csv", function(data) {
  data.forEach(function(d) {
   
   // d["faculty salary"] = +d["faculty salary"]
    
});

  var xMax2 = d3.max(data, function(d) { return d[xCat2]; }) * 1.05,
      xMin2 = d3.min(data, function(d) { return d[xCat2]; }),
      xMin2 = xMin2 > 0 ? 0 : xMin2,
      yMax2 = d3.max(data, function(d) { return d[yCat2]; }) * 1.05,
      yMin2 = d3.min(data, function(d) { return d[yCat2]; }),
      yMin2 = yMin2 > 0 ? 0 : yMin2;

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
     return d[myname] + "<br>" + "Branches: " + d[mybranches] + "<br>" + d[colorCat]; 
     });

  var zoomBeh = d3.behavior.zoom()
      .x(x)
      .y(y)
      .scaleExtent([0, 500])
      .on("zoom", zoom);

  var svg2 = d3.select("#scatter2")
    .append("svg")
      .attr("width", outerWidth)
      .attr("height", outerHeight)
    .append("g")
      .attr("transform", "translate(" + margin.left + "," + margin.top + ")")
      .call(zoomBeh);

  svg2.call(tip);

  svg2.append("rect")
      .attr("width", width)
      .attr("height", height);

  svg2.append("g")
      .classed("x axis", true)
      .attr("transform", "translate(0," + height + ")")
      .call(xAxis)
    .append("text")
      .classed("label", true)
      .attr("x", width)
      .attr("y", margin.bottom -25)
      .style("text-anchor", "end")
      .text(xCat2);

  svg2.append("g")
      .classed("y axis", true)
      .call(yAxis)
    .append("text")
      .classed("label", true)
      .attr("transform", "rotate(-90)")
      .attr("y", -margin.left)
      .attr("dy", ".71em")
      .style("text-anchor", "end")
      .text(yCat2);

  svg2.append("g")
  .append("text")
  .classed("label", true)
    .attr("x", width / 2 )
    .attr("y", -10)
    .style("text-anchor", "middle")
    .text("Tuition Revenue vs. Faculty Salary");



  var objects = svg2.append("svg")
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
      .attr("r", function (d) { return 6*Math.sqrt(d[rCat] / Math.PI); })
     //.attr("r", function (d) { return d[rCat]; })
      .attr("transform", transform)
      .style("fill", function(d) { return color(d[colorCat]); })
      .on("mouseover", tip.show)
      .on("mouseout", tip.hide);

  var legend = svg2.selectAll(".legend")
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
    svg2.select(".x.axis").call(xAxis);
    svg2.select(".y.axis").call(yAxis);

    svg2.selectAll(".dot")
        .attr("transform", transform);
  }

  function transform(d) {
    return "translate(" + x(d[xCat2]) + "," + y(d[yCat2]) + ")";
  }
});

</script>


Another very stark difference between for-profit and non-profit schools are the types of financing their students receive. Non-profit school students typically complete the Fafsa application, which initiates the process of receiving a federal loan. A federal student loan is borrowed money that must be paid back with interest. For-profit schools, however, have a much larger percentage of their students receiving Pell grants. A Pell grant, unlike a loan, does not need to be repaid. Loosely, the US taxpayers are giving this money directly to for-profit schools and will never be repaid. You can see the difference between for-profit and non-profit student financing here. Everest College, with a whopping 55 branches, is taking among the largest percentage of Pell grant money:


<meta charset="utf-8">
<style>

rect {
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

<div id="scatter3"></div>
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


var xCat3 = "percent fafsa";
   yCat3 = "percent pell grant";
    rCat = "branches";
    myname = "schoolname";
    colorCat = "type";
    mybranches = "branches";
    under_invest = "under investigation";


d3.csv("/myschools.csv", function(data) {
  data.forEach(function(d) {
   
   // d["faculty salary"] = +d["faculty salary"]
    
});

  var xMax3 = d3.max(data, function(d) { return d[xCat3]; }) * 1.05,
      xMin3 = d3.min(data, function(d) { return d[xCat3]; }),
      xMin3 = xMin3 > 0 ? 0 : xMin3,
      yMax3 = d3.max(data, function(d) { return d[yCat3]; }) * 1.05,
      yMin3 = d3.min(data, function(d) { return d[yCat3]; }),
      yMin3 = yMin3 > 0 ? 0 : yMin3;

  x.domain([0, 1]);
  y.domain([0, 1]);
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

     return d[myname] + "<br>" + "Branches: " + d[mybranches] + "<br>" + d[colorCat]; 
     });

  var zoomBeh = d3.behavior.zoom()
      .x(x)
      .y(y)
      .scaleExtent([0, 500])
      .on("zoom", zoom);

  var svg3 = d3.select("#scatter3")
    .append("svg")
      .attr("width", outerWidth)
      .attr("height", outerHeight)
    .append("g")
      .attr("transform", "translate(" + margin.left + "," + margin.top + ")")
      .call(zoomBeh);

  svg3.call(tip);

  svg3.append("rect")
      .attr("width", width)
      .attr("height", height);

  svg3.append("g")
      .classed("x axis", true)
      .attr("transform", "translate(0," + height + ")")
      .call(xAxis)
    .append("text")
      .classed("label", true)
      .attr("x", width)
      .attr("y", margin.bottom -25)
      .style("text-anchor", "end")
      .text(xCat3);

  svg3.append("g")
      .classed("y axis", true)
      .call(yAxis)
    .append("text")
      .classed("label", true)
      .attr("transform", "rotate(-90)")
      .attr("y", -margin.left)
      .attr("dy", ".71em")
      .style("text-anchor", "end")
      .text(yCat3);

  svg3.append("g")
  .append("text")
  .classed("label", true)
    .attr("x", width / 2 )
    .attr("y", -10)
    .style("text-anchor", "middle")
    .text("% of students submitting fafsa apps vs % of students receiving pell grants");



  var objects = svg3.append("svg")
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
      .attr("r", function (d) { return 6*Math.sqrt(d[rCat] / Math.PI); })
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

  var legend = svg3.selectAll(".legend")
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
    svg3.select(".x.axis").call(xAxis);
    svg3.select(".y.axis").call(yAxis);

    svg3.selectAll(".dot")
        .attr("transform", transform);
  }

  function transform(d) {
    return "translate(" + x(d[xCat3]) + "," + y(d[yCat3]) + ")";
  }
});

</script>




Lastly, there is also a stark difference in repayment rates of student loans between for and non-profit schools. For-profit schools have a much lower percentage of students with a declining loan balance 5 years after loan origination. In addition, they have a very low percentage of students who have repaid their loan in full seven years out:


<meta charset="utf-8">
<style>

rect {
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



<div id="scatter4"></div>
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


var xCat4 = "5 year declining balance";
    yCat4 = "7 yr repayment completion";
    rCat = "branches";
    myname = "schoolname";
    colorCat = "type";
    mybranches = "branches";
    under_invest = "under investigation";


d3.csv("/myschools.csv", function(data) {
  data.forEach(function(d) {
   
   // d["faculty salary"] = +d["faculty salary"]
    
});

  var xMax4 = d3.max(data, function(d) { return d[xCat4]; }) * 1.05,
      xMin4 = d3.min(data, function(d) { return d[xCat4]; }),
      xMin4 = xMin4 > 0 ? 0 : xMin4,
      yMax4 = d3.max(data, function(d) { return d[yCat4]; }) * 1.05,
      yMin4 = d3.min(data, function(d) { return d[yCat4]; }),
      yMin4 = yMin4 > 0 ? 0 : yMin4;


  x.domain([0, 1]);
  y.domain([0, 1]);

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

  var svg4 = d3.select("#scatter4")
    .append("svg")
      .attr("width", outerWidth)
      .attr("height", outerHeight)
    .append("g")
      .attr("transform", "translate(" + margin.left + "," + margin.top + ")")
      .call(zoomBeh);

  svg4.call(tip);

  svg4.append("rect")
      .attr("width", width)
      .attr("height", height);

  svg4.append("g")
      .classed("x axis", true)
      .attr("transform", "translate(0," + height + ")")
      .call(xAxis)
    .append("text")
      .classed("label", true)
      .attr("x", width)
      .attr("y", margin.bottom -25)
      .style("text-anchor", "end")
      .text(xCat4);

  svg4.append("g")
      .classed("y axis", true)
      .call(yAxis)
    .append("text")
      .classed("label", true)
      .attr("transform", "rotate(-90)")
      .attr("y", -margin.left)
      .attr("dy", ".71em")
      .style("text-anchor", "end")
      .text(yCat4);

  svg4.append("g")
  .append("text")
  .classed("label", true)
    .attr("x", width / 2 )
    .attr("y", -10)
    .style("text-anchor", "middle")
    .text("% of students with declining loan balances vs % of students with loan repayment completition");



  var objects = svg4.append("svg")
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
      .attr("r", function (d) { return 6*Math.sqrt(d[rCat] / Math.PI); })
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

  var legend = svg4.selectAll(".legend")
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
    svg4.select(".x.axis").call(xAxis);
    svg4.select(".y.axis").call(yAxis);

    svg4.selectAll(".dot")
        .attr("transform", transform);
  }

  function transform(d) {
    return "translate(" + x(d[xCat4]) + "," + y(d[yCat4]) + ")";
  }
});

</script>


How easily can for-profit schools and non-profit schools be distinguished? Well, if I use just four features that are extremely important to student education and faculty well-being (tuition revenue per fte, instructional expenditure per fte, 5 year declining loan balance, and faculty salary), then I can predict non-profit and for-profit status with 99% accuracy:


<img src="/images/forprofit.png" width="500"/> 



In summary, here are four take-aways from my project:

1. Using extreme gradient boosting, we have a model that detects 79% of schools receiving failing debt-to-earning scores.

2. Of the schools that our model predicts to be failing, we are correct 93% of the time.

3. The Department of Education may want to impose more regulation on for-profit schools, as they account for 97% of the failing schools.

4. For-profit and non-profit schools differ significantly in how much tuition revenue they collect from students, how much revenue they dedicate towards instruction, how much they pay their teachers, how much Pell grant money they take from taxpayers, and how quickly their alumni are able to pay back their loans.

I had a blast doing this project! I learned a ton about using scikit-learn classification algorithms and D3 visualization. I also used stored my data on an AWS server and accessed it using PostgreSQL. You can find my project code [here](https://github.com/laurenshareshian/Detecting_For_Profit_College_Fraud).

