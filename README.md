CS171 - lab 4 - layouts
====

### Introduction

Previously in CS171:

* [Lecture 4](http://cm.dce.harvard.edu/2014/02/24028/L04/screen_H264MultipleHighLTH-16x9.shtml) on visual variables
* [Lecture 6](http://cm.dce.harvard.edu/2014/02/24028/L06/screen_H264MultipleHighLTH-16x9.shtml) on graphs
* D3's [documentation](https://github.com/mbostock/d3/wiki/Layouts) on layouts
* [Homework 2](https://github.com/CS171/HW2)

Keep in mind that a layout drawing with D3 can be divided into those two parts:

* *Layout-related* data creation and binding (e.g. position of the points) with [`d3.layout()`](https://github.com/mbostock/d3/wiki/API-Reference#wiki-d3layout-layouts) functions
* `SVG` element drawing (often a `<path>` element) with [`d3.svg`](https://github.com/mbostock/d3/wiki/API-Reference#wiki-d3svg-svg) functions

### Force-directed layout

* Some layouts examples have been [presented](http://bl.ocks.org/mbostock/4062045) during the class. Look at the end of this document for more sophisticated ones. 
* The underlying layout is well [documentated](https://github.com/mbostock/d3/wiki/Force-Layout) in the D3 wiki

Let's see how a force-layout works in D3. 
* Open [Homework 2](https://github.com/CS171/HW2) code template
* Look at the design variations
* Investigate how nodes are created
  * Nodes `graph.nodes` and `graph.links`
  * Layout function `d3.layout.force()` applied to the nodes 
    * By creating `x` and `y` attribuets
    * And updating them

**Adding more nodes**

* To dynamically add new nodes:
  * Update the `graph.nodes` array
  * Update the `force.nodes()` layout
  * Re-bind the data and add new circles
* Let's create new nodes for each mouse move at the [pointer position](https://github.com/mbostock/d3/wiki/Selections#wiki-d3_mouse)

```javascript
d3.select("svg").on("mousemove", function(d, i) {
  graph.nodes.push({x:d3.mouse(this)[0], y:d3.mouse(this)[1], cat:Math.floor(nb_cat*Math.random())})
  force.nodes(graph.nodes).start();
  node = node.data(graph.nodes);
  node.enter()
      .append("g").attr("class", "node")
      .append("circle")
      .attr("r", 5)
})
```

* Color by category `.style("fill", function(d) { return fill(d.cat); })`

**The `.tick()` function**

* Computes the velocity decay and position; can be manually triggered
  * Run in the console `force.start();force.tick();force.stop();`
  * Test a `.tick()` [loop](https://github.com/mbostock/d3/wiki/Force-Layout#wiki-tick) at the init of the page to pre-generate the layout

```javascript
var n = 50;
force.start();
for (var i = 0; i < n; ++i) force.tick();
force.stop();
```

**Layout division into foci**

* The `.tick()` function can also be overrided for custom layouts
* Example of a muliti-foci layout:

```javascript
var foci = [{x: 150, y: 150}, {x: 350, y: 250}, {x: 700, y: 400}];

function tick(e) {
  var k = .1 * e.alpha;
  graph.nodes.forEach(function(o, i) {
    o.x += (foci[o.id%foci.length].x - o.x) * k;
    o.y += (foci[o.id%foci.length].y - o.y) * k;
  });

  graph_update(0);
}
```
* Disable links
* Show nodes color to reveal categories cluster `d3.selectAll("circle").style("fill", function(d) { return fill(d.cat); })`

### Diving deeper into D3 layouts

* As mentionned earlier, generating the layout is different than drawing it
* For instance, look at this [tree layout](http://mbostock.github.io/d3/talk/20111018/tree.html)

**Understanding `SVG` shape drawing functions**

* Documentation for all the [SVG shapes](https://github.com/mbostock/d3/wiki/API-Reference#wiki-d3svg-svg) D3 can draw
* Most of them return `<path>` elements

**Example with a diagonal element*

```javascript
var svg = d3.select("body").append("svg")
    .attr("width", 500)
    .attr("height", 500)

var diagonal = d3.svg.diagonal() 
    .source({x: 10, y: 10})
    .target({x: 300, y: 300})

svg.append("path")
    .attr("fill", "none")
    .attr("stroke", "steelblue")
    .attr("d", diagonal)
```

* As a reminder, a line in SVG

```javascript
svg.selectAll(".line")
    .data([{source: {x: 10, y: 10}, target:{x: 300, y: 300}}])
    .enter().append("line")
    .attr("stroke", "steelblue")
    .attr("x1", function(d) { return d.source.x; })
    .attr("y1", function(d) { return d.source.y; })
    .attr("x2", function(d) { return d.target.x; })
    .attr("y2", function(d) { return d.target.y; })
```

* SVG function to draw a line

```javascript
var data = [{source: {x: 10, y: 10}, target:{x: 300, y: 300}}];

var line = d3.svg.line()
            .x(function(d) { return d.x; })
            .y(function(d) { return d.y; })
            .interpolate("linear");

svg.append("path")
        .attr("d", line(d3.values(data[0])))
        .attr("stroke", "red")
        .attr("stroke-width", 1)
        .attr("fill", "none");
```

* Using the diagonal function

```javascript
var diagonal = d3.svg.diagonal() 
       .source(data[0].source)
       .target(data[0].target)

 svg.append("path")
     .attr("fill", "none")
     .attr("stroke", "steelblue")
     .attr("d", diagonal)
```

* Makes drawing easier!

### Tree layout

* Let's use those SVG functions to draw the graph
* But before, generate a tree-layout with the points

* [Example](http://mbostock.github.io/d3/talk/20111018/tree.html)

**Data Structure**

**Vertical/Horizontal tree layout**

**Radial tree layout**

### Pie chart layout

* [Documentation](https://github.com/mbostock/d3/wiki/Pie-Layout)
* [Arc Shape](https://github.com/mbostock/d3/wiki/SVG-Shapes#wiki-arc)

### Histogram layout

* [Documentation](https://github.com/mbostock/d3/wiki/Histogram-Layout)
* [Example](http://bl.ocks.org/mbostock/3048450)

### Nested layout

Nested layouts, they belong to the [hierarchical layouts](https://github.com/mbostock/d3/wiki/Hierarchy-Layout) family

* Treemap
* Circle packing

### Breakdown of complex examples

* [Force-Directed Symbols](http://bl.ocks.org/mbostock/1062383)
* [Hive plot](http://bost.ocks.org/mike/hive/)
* [Collision detection](http://mbostock.github.io/d3/talk/20111018/collision.html)
* [D3.js world map with force layout](http://bl.ocks.org/bycoffe/3230965)
* [Force directed states](http://mbostock.github.io/d3/talk/20111018/force-states.html)
* [Collapsible nodes](http://mbostock.github.io/d3/talk/20111116/force-collapsible.html)
