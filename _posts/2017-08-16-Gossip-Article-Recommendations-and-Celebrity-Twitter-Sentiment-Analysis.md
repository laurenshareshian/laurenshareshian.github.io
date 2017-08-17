---
layout: post
Title: TMZ Article Recommendations and Celebrity Twitter Sentiment Analysis
---
<img src="/images/mugshots.png" width="600"/> 

I admit it, it's a major character flaw of mine, but I love celebrity gossip. TMZ, US Weekly, you name it, I gotta have it. So, for our Natural Language Processing unit, I wanted to explore all things celebrity gossip. 

My first objective was to find the hottest celebrity gossip topics of the summer. To do this, I scraped TMZ for all 1,507 articles published between June 9th and August 9th, 2017. I first tried using Selenium to grab the articles, but after a whole night of scraping and only 500 articles downloaded (due to the crazy slow loading of TMZ pages with all of their advertisements and photos) I realized that curling all of the articles was the way to go. It took less than a minute! From there, I used BeautifulSoup to collect the relevant text.

To pull out the most popular topics, I made a sci-kit learn pipeline that implemented TFIDF vectorization (and  automatically removed stop words and discarded numbers and punctuation, too), a Truncated SVD for dimensionality reduction, and normalization. The ten most prominent topics are below. 

<img src="/images/hottopics.png" width="500"/> 

As you can see above, the first topic is rather nonsensical. I would have guessed that "says", "got", "told", and "just" were stop words that would have been removed by the sci-kit learn package, but apparently not. However, the next several topics are spot on. The scandalous Rob Kardashian - Blac Chyna breakup over her affair with Ferrari and the subsequent pics that got leaked were a hot topic. In addition, the Corrine-Demario Bachelor in Paradise pool scandal was big news. Of course, Justin Bieber canceling his tour because he found his purpose at church was another big headline. Notice that Chester appeared in three different topics. It makes sense that the suicide of Chester Bennington would appear in Topic 7 related his band, Linkin Park, and the suicide of his friend, Chris Cornell, but it was a bit puzzling that his name appeared in three different topics. 


Okay. My second objective was to find articles similar to an article of interest in order to suggest recommendations to TMZ readers. To do this, I implemented Gensim's similarity matrix that measured cosine distance between vectors. Using this algorithm, I found that if you really liked [this article](http://www.tmz.com/2017/08/09/usher-jermaine-dupri-new-music-herpes-allegations/) on Usher's herpes scandal, for example, then you would most like these other articles:

<img src="/images/similararticles.png" width="700"/> 

My third objective was to use unsupervised learning techniques to cluster articles. The algorithm DBSCAN chooses how many clusters to use. It chose 13, and so I also used 13 K-Means clusters for comparison. Hands down, K-Means was the superior clustering algorithm for this data set. You can see clear structure in the second picture:

<img src="/images/dbscan.png" width="450"/> <img src="/images/kmeans.png" width="450"/> 


My fourth objective was to visualize how close some of the hottest summer topics were to each other using a two dimensional t-SNE plot. Remember, I had 1,507 documents and after removing stopwords and words that appeared less than 10 times, I had a 1,507 x 1,640 doc-word matrix. Thus, the fact that the t-SNE algorithm allows dimensionality reduction that preserves distance but allows me to visualize things in a simple 2D plane is super cool! Below, you can use the D3 visualization to view the proximity of articles containing the following hot topics:

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


var xCat = "x";
    yCat = "y";
    myname = "article";
    colorCat = "celeb";


d3.csv("/mycelebs.csv", function(data) {
  data.forEach(function(d) {
   
});

  var xMax1 = d3.max(data, function(d) { return d[xCat]; }) * 1.05,
      xMin1 = d3.min(data, function(d) { return d[xCat]; }),
      xMin1 = xMin1 > 0 ? 0 : xMin1,
      yMax1 = d3.max(data, function(d) { return d[yCat]; }) * 1.05,
      yMin1 = d3.min(data, function(d) { return d[yCat]; }),
      yMin1 = yMin1 > 0 ? 0 : yMin1;

  x.domain([-25,25]);
  y.domain([-25,25]);

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
  .domain(['rob & chyna', 'kim & kanye', 'justin bieber', 'trump', 'bachelor pool scandal', 'mcgregor mayweather fight', 'chester & chris cornell suicides', 'usher herpes scandal', 'cosby trial', 'o.j. simpson'])
  .range(["pink", "gray", "orange", "green", "blue", "purple", "red", "black", "cyan", "yellow"]);

d3.tip = function() {
  var direction = d3_tip_direction,
      offset    = d3_tip_offset,
      html      = d3_tip_html,
      node      = initNode(),
      svg       = null,
      point     = null,
      target    = null

  function tip(vis) {
    svg = getSVGNode(vis)
    point = svg.createSVGPoint()
    document.body.appendChild(node)
  }

  // Public - show the tooltip on the screen
  //
  // Returns a tip
  tip.show = function() {
    var args = Array.prototype.slice.call(arguments)
    if(args[args.length - 1] instanceof SVGElement) target = args.pop()

    var content = html.apply(this, args),
        poffset = offset.apply(this, args),
        dir     = direction.apply(this, args),
        nodel   = d3.select(node), i = 0,
        coords

    nodel.html(content)
      .style({ opacity: 1, 'pointer-events': 'all' })

    while(i--) nodel.classed(directions[i], false)
    coords = direction_callbacks.get(dir).apply(this)
    nodel.classed(dir, true).style({
      top: (coords.top +  poffset[0]) + 'px',
      left: (coords.left + poffset[1]) + 'px'
    })

    return tip
  }

  // Public - hide the tooltip
  //
  // Returns a tip
  tip.hide = function() {
    nodel = d3.select(node)
    nodel.style({ opacity: 0, 'pointer-events': 'none' })
    return tip
  }

  // Public: Proxy attr calls to the d3 tip container.  Sets or gets attribute value.
  //
  // n - name of the attribute
  // v - value of the attribute
  //
  // Returns tip or attribute value
  tip.attr = function(n, v) {
    if (arguments.length < 2 && typeof n === 'string') {
      return d3.select(node).attr(n)
    } else {
      var args =  Array.prototype.slice.call(arguments)
      d3.selection.prototype.attr.apply(d3.select(node), args)
    }

    return tip
  }

  // Public: Proxy style calls to the d3 tip container.  Sets or gets a style value.
  //
  // n - name of the property
  // v - value of the property
  //
  // Returns tip or style property value
  tip.style = function(n, v) {
    if (arguments.length < 2 && typeof n === 'string') {
      return d3.select(node).style(n)
    } else {
      var args =  Array.prototype.slice.call(arguments)
      d3.selection.prototype.style.apply(d3.select(node), args)
    }

    return tip
  }

  // Public: Set or get the direction of the tooltip
  //
  // v - One of n(north), s(south), e(east), or w(west), nw(northwest),
  //     sw(southwest), ne(northeast) or se(southeast)
  //
  // Returns tip or direction
  tip.direction = function(v) {
    if (!arguments.length) return direction
    direction = v == null ? v : d3.functor(v)

    return tip
  }

  // Public: Sets or gets the offset of the tip
  //
  // v - Array of [x, y] offset
  //
  // Returns offset or
  tip.offset = function(v) {
    if (!arguments.length) return offset
    offset = v == null ? v : d3.functor(v)

    return tip
  }

  // Public: sets or gets the html value of the tooltip
  //
  // v - String value of the tip
  //
  // Returns html value or tip
  tip.html = function(v) {
    if (!arguments.length) return html
    html = v == null ? v : d3.functor(v)

    return tip
  }

  function d3_tip_direction() { return 'n' }
  function d3_tip_offset() { return [0, 0] }
  function d3_tip_html() { return ' ' }

  var direction_callbacks = d3.map({
    n:  direction_n,
    s:  direction_s,
    e:  direction_e,
    w:  direction_w,
    nw: direction_nw,
    ne: direction_ne,
    sw: direction_sw,
    se: direction_se
  }),

  directions = direction_callbacks.keys()

  function direction_n() {
    var bbox = getScreenBBox()
    return {
      top:  bbox.n.y - node.offsetHeight,
      left: bbox.n.x - node.offsetWidth / 2
    }
  }

  function direction_s() {
    var bbox = getScreenBBox()
    return {
      top:  bbox.s.y,
      left: bbox.s.x - node.offsetWidth / 2
    }
  }

  function direction_e() {
    var bbox = getScreenBBox()
    return {
      top:  bbox.e.y - node.offsetHeight / 2,
      left: bbox.e.x
    }
  }

  function direction_w() {
    var bbox = getScreenBBox()
    return {
      top:  bbox.w.y - node.offsetHeight / 2,
      left: bbox.w.x - node.offsetWidth
    }
  }

  function direction_nw() {
    var bbox = getScreenBBox()
    return {
      top:  bbox.nw.y - node.offsetHeight,
      left: bbox.nw.x - node.offsetWidth
    }
  }

  function direction_ne() {
    var bbox = getScreenBBox()
    return {
      top:  bbox.ne.y - node.offsetHeight,
      left: bbox.ne.x
    }
  }

  function direction_sw() {
    var bbox = getScreenBBox()
    return {
      top:  bbox.sw.y,
      left: bbox.sw.x - node.offsetWidth
    }
  }

  function direction_se() {
    var bbox = getScreenBBox()
    return {
      top:  bbox.se.y,
      left: bbox.e.x
    }
  }

  function initNode() {
    var node = d3.select(document.createElement('div'))
    node.style({
      position: 'absolute',
      opacity: 0,
      pointerEvents: 'none',
      boxSizing: 'border-box'
    })

    return node.node()
  }

  function getSVGNode(el) {
    el = el.node()
    if(el.tagName.toLowerCase() == 'svg')
      return el

    return el.ownerSVGElement
  }

  // Private - gets the screen coordinates of a shape
  //
  // Given a shape on the screen, will return an SVGPoint for the directions
  // n(north), s(south), e(east), w(west), ne(northeast), se(southeast), nw(northwest),
  // sw(southwest).
  //
  //    +-+-+
  //    |   |
  //    +   +
  //    |   |
  //    +-+-+
  //
  // Returns an Object {n, s, e, w, nw, sw, ne, se}
  function getScreenBBox() {
    var targetel   = target || d3.event.target,
        bbox       = {},
        matrix     = targetel.getScreenCTM(),
        tbbox      = targetel.getBBox(),
        width      = tbbox.width,
        height     = tbbox.height,
        x          = tbbox.x,
        y          = tbbox.y,
        scrollTop  = document.documentElement.scrollTop || document.body.scrollTop,
        scrollLeft = document.documentElement.scrollLeft || document.body.scrollLeft


    point.x = x + scrollLeft
    point.y = y + scrollTop
    bbox.nw = point.matrixTransform(matrix)
    point.x += width
    bbox.ne = point.matrixTransform(matrix)
    point.y += height
    bbox.se = point.matrixTransform(matrix)
    point.x -= width
    bbox.sw = point.matrixTransform(matrix)
    point.y -= height / 2
    bbox.w  = point.matrixTransform(matrix)
    point.x += width
    bbox.e = point.matrixTransform(matrix)
    point.x -= width / 2
    point.y -= height / 2
    bbox.n = point.matrixTransform(matrix)
    point.y += height
    bbox.s = point.matrixTransform(matrix)

    return bbox
  }

  return tip
};

  var tip = d3.tip()
      .attr("class", "d3-tip")
      .offset([-10, 0])
      .html(function(d) {
       console.log(d);
      //  return xCat + ": " + d[xCat] + "<br>" + yCat + ": " + d[yCat];
     d[myname]; 
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
    .text("t-SNE dimensionality reduction plot");



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
      .attr("r", function (d) { return 5; })
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
    return "translate(" + x(d[xCat]) + "," + y(d[yCat]) + ")";
  }
});

</script>
Some characteristics of the t-SNE plot made intuitive sense. The blue Bachelor and black Usher points were near each other, probably as they both related to sex scandals. Similarly, the yellow O.J. Simpson and cyan Bill Cosby points were nearby, as they dealt with legal trials. However, I was surprised that the pink Rob Kardashian points weren't closer to the Kim Kardashian grey points, as they are relatives.

You might be wondering which celebrity names appeared most frequently in TMZ articles in the summer of 2017. Well, you could visualize the frequency using a fancy D3 word cloud:




<html>
  <head>
    <script src="/d3.v2.js"></script>
    <script src="/d3.layout.cloud.js"></script>
    <script src="/gossip.js"></script>
  </head>
  <body>
    <div id="chart"></div>
<script>

  var svg_location = "#chart";


var fill = d3.scale.category20();

d3.layout.cloud().size([700, 700])
      .words(gossip)
      .rotate(function() { return ~~(Math.random() * 2) * 90; })
      .font("Impact")
      .fontSize(function(d) { return d.size; })
      .on("end", draw)
      .on("end", draw)
      .start();

function draw(words) {
    d3.select(svg_location).append("svg")
        .attr("width", 700)
        .attr("height", 700)
      .append("g")
        .attr("transform", "translate(350,350)")
      .selectAll("text")
        .data(words)
      .enter().append("text")
        .style("font-size", function(d) { return d.size + "px"; })
        .style("font-family", "Impact")
        .style("fill", function(d, i) { return fill(i); })
        .attr("text-anchor", "middle")
        .attr("transform", function(d) {
          return "translate(" + [d.x, d.y] + ")rotate(" + d.rotate + ")";
        })
        .text(function(d) { return d.text; });
  }
  </script>
</body>
</html>


Or, you could view it (my preferred) old-fashioned way, in a list:

<img src="/images/frequent.png" width="200"/> 

My last objective was to use scikit-learn's NLTK Vader library to do some sentiment analysis on the most popular [celebrity twitter accounts](http://friendorfollow.com/twitter/most-followers/). Out of all of the celebs listed, who was the most positive? Neil Patrick Harris, with an average sentiment score of 0.4254. 


<img src="/images/neil.png" width="200"/> 

His tweets are hilarious - they are all SOOOOOO upbeat that you kind of want to smack him:


<img src="/images/neiltweets.png" width="1200"/> 


Who were the most negative celebrities? Chris Brown and Snoop Dogg. Chris Brown didn't surprise me, but Snoop Dogg sure did. He seems like a fun fellow. WHy did he only have an average sentiment score of 0.06?


<img src="/images/snoop.png" width="200"/> 


Well, upon closer examination, the sentiment analyzer really doesn't pick up on his language subtleties. "This is the shit" is considered a bad thing:


<img src="/images/snooptweets.png" width="700"/> 



In summary, I had a blast doing this project. While the topic of celebrity gossip was rather silly, it was broad enough to allow me to investigate many different areas of Natural Language Processing, and in a context in which I already had intitution for what the results of the model should give me. You can find my GitHub repo for the project [here](https://github.com/laurenshareshian/Celebrity_NLP).
