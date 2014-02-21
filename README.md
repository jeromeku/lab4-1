CS171 - lab 4 - layouts
====

### Introduction

Previously on CS171:

* [Lecture 4](http://cm.dce.harvard.edu/2014/02/24028/L04/screen_H264MultipleHighLTH-16x9.shtml) on visual variables
* [Lecture 6](http://cm.dce.harvard.edu/2014/02/24028/L06/screen_H264MultipleHighLTH-16x9.shtml) on graph layouts
* [Homework 2](https://github.com/CS171/HW2)

Before getting started, keep in mind drawing layouts with D3 is divided in two parts:

* Layout data creation and binding (e.g. position of the points) with [`d3.layout()`](https://github.com/mbostock/d3/wiki/API-Reference#wiki-d3layout-layouts) functions
* Using `SVG` element to *visually represent* the layout (often a `<path>` element) with [`d3.svg`](https://github.com/mbostock/d3/wiki/API-Reference#wiki-d3svg-svg) functions

Layouts are very-well documented:

* D3's [documentation](https://github.com/mbostock/d3/wiki/Layouts) on layouts

### Force-directed layout

* Some layouts examples have been [presented](http://bl.ocks.org/mbostock/4062045) during the class. Look at the end of this document for other examples. 
* Look at the [documentation](https://github.com/mbostock/d3/wiki/Force-Layout) in the D3 wiki

Let's see how a force-layout works in D3. 
* Open [Homework 2](https://github.com/CS171/HW2) code template
* Look at the design variations
* Investigate how nodes `graph.nodes` and `graph.links` are created 
* Look at the layout function `d3.layout.force()` applied to the nodes 
  * It creates `x` and `y` attributes (if they don't exist)
  * And updates them until the simulation reaches a stable state

**Adding more nodes**

* To dynamically add new nodes:
  * Update the `graph.nodes` array
  * Update the `force.nodes()` layoutd
  * Re-bind the data and add new circles
* For fun, let's create new nodes for each mouse move at the [pointer position](https://github.com/mbostock/d3/wiki/Selections#wiki-d3_mouse)

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

* Color the nodes by category `.style("fill", function(d) { return fill(d.cat); })`

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
* Example of a multi-foci layout:

```javascript
var foci = [{x: 150, y: 150}, {x: 350, y: 250}, {x: 700, y: 400}];

function tick(e) {
  var k = .2 * e.alpha;
  graph.nodes.forEach(function(d, i) {
    d.x += (foci[d.id%foci.length].x - d.x) * k;
    d.y += (foci[d.id%foci.length].y - d.y) * k;
  });

  graph_update(0);
}
```
* `alpha` is the [cooling parameter\(https://github.com/mbostock/d3/wiki/Force-Layout#wiki-alpha) of the layout
* Disable links to let nodes propagate correctly
* Show nodes color to reveal categories cluster `d3.selectAll("circle").style("fill", function(d) { return fill(d.cat); })`

### Diving deeper into D3 layouts

* As mentionned earlier, generating the layout is different than drawing it
* For instance, look at this [tree layout](http://mbostock.github.io/d3/talk/20111018/tree.html)

**Understanding `SVG` shape drawing functions**

* Documentation for all the [SVG shapes](https://github.com/mbostock/d3/wiki/API-Reference#wiki-d3svg-svg) D3 can draw
* Most of them return `<path>` elements
* As a reminder, a line in SVG:

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

* Alternatively, using the D# SVG function to draw a line:

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

* Using the diagonal function, but we need to provide it with some data
* The path generator expects the source and target points of the point

```javascript
var diagonal = d3.svg.diagonal() 
       .source(data[0].source)
       .target(data[0].target)

 svg.append("path")
     .attr("fill", "none")
     .attr("stroke", "steelblue")
     .attr("d", diagonal)
```

* You can also bind the data

```javascript
var diagonal = d3.svg.diagonal() 

svg.selectAll(".diag").data(data).enter().append("path").attr("class", "diag")
    .attr("fill", "none")
    .attr("stroke", "steelblue")
    .attr("d", diagonal)
```

### Tree layout

* Let's use those SVG functions to draw the [tree](http://mbostock.github.io/d3/talk/20111018/tree.html)
* But before, generate a tree-layout with the points

**Data Structure**

* D3 layouts requires some specific data structure. For trees:

```json
{
  "name": "Root",
  "children": [
   {
    "name": "AA",
    "children": [
     {"name": "AB", "size": 1, 
       "children": [
       {"name": "AAA", "size": 2},
       {"name": "AAB", "size": 3},
       {"name": "AAC", "size": 4}
         ]},
     {"name": "C", "size": 5},
     {"name": "D", "size": 6},
     {"name": "E", "size": 7}
    ]
   }]
 };
```

* Note that the root node is the first node of the data
* Generate the layouts of the nodes

```javascript 
var tree = d3.layout.tree()
              .size([300,250]);

var nodes = tree.nodes(data);
var links = tree.links(nodes);
```

* Oh wait, didn't we just generate data??
* Let's bind them to circles!

```javascript 
var node = svg.selectAll("g.node")
               .data(nodes)
               .enter().append("g")
               .attr("transform", function(d) { return "translate(" + d.x + "," + d.y + ")"; })  

 node.append("circle")
     .attr("r", function(d) { return d.children ? d.children.length : d.size;})
     .attr("fill", "steelblue")
```

* Let's now link the nodes with a SVG line

```javascript 
var line = svg.selectAll(".line")
              .data(links)
              .enter().append("line")
              .attr("class", "line")
              .attr("x1", function(d) { return d.source.x; })
              .attr("y1", function(d) { return d.source.y; })
              .attr("x2", function(d) { return d.target.x; })
              .attr("y2", function(d) { return d.target.y; })
```

* Better use a `d3.svg.diagonal()` SVG function as seen before
* The magic happens within `tree.links(nodes)`

```javascript 
var link = svg.selectAll(".link")
              .data(links)
              .enter().append("path")
              .attr("class", "link")
              .attr("d", diagonal)
```

**Vertical/Horizontal tree layout**

* Change both layout and SVG drawing parameters to make it horizontal
  * Flip the x and y coordinate for the circle nodes
  * For the path, it's more tricky
  * Create an accessor function       

```javascript
var diagonal_rotated = d3.svg.diagonal().projection(function(d) { return [d.y, d.x]});

link.transition().delay(1000).duration(1000)
    .attr("d", diagonal_rotated)
```

* Adjust the node labels, show the `d.depth`

**Radial tree layout**

* Switch to a radial layout with a `.separation()`

```javascript
var tree = d3.layout.tree()
    .size([360, diameter / 2 - 120])
    .separation(function(a, b) { return (a.parent == b.parent ? 1 : 2) / a.depth; });
```

* Update the nodes with a `rotate()` transform

```javascript
node.attr("transform", function(d) { return "rotate(" + (d.x - 90) + ") translate(" + d.y + ")";})
```

* Update the diagonal function as well

```javascript
var diagonal = d3.svg.diagonal.radial()
   .projection(function(d) { return [d.y, d.x / 180 * Math.PI]; });
```
* Your turn: Implement the transition with the non-radial layout!
* Test with `d3.layout.cluster()` layout (instead of `d3.layout.tree()`)

### Pie chart layout

* [Documentation](https://github.com/mbostock/d3/wiki/Pie-Layout)
* [Arc Shape](https://github.com/mbostock/d3/wiki/SVG-Shapes#wiki-arc)

### Histogram layout

* [Documentation](https://github.com/mbostock/d3/wiki/Histogram-Layout)
* [Example](http://bl.ocks.org/mbostock/3048450)

### Nested layout

Nested layouts, they belong to the [hierarchical layouts](https://github.com/mbostock/d3/wiki/Hierarchy-Layout) family

* Treemap
  * [Documentation](https://github.com/mbostock/d3/wiki/Treemap-Layout)
  * [Example](http://bl.ocks.org/mbostock/4063582)
* Circle packing
  * [Example](http://bl.ocks.org/mbostock/4063530) 
  * [Documentation](https://github.com/mbostock/d3/wiki/Pack-Layout)
  * To use the `d3.layout.pack()` data need to have a value attribute
  * Radius of circles can encode various things: depth, .. 
  * You can make a [bubble chart](http://bl.ocks.org/mbostock/4063269) by keeping the leave nodes `.attr("opacity", function(d) { return d.children ? 0 : .2;})`

### Breakdown of complex examples

* [Force-Directed Symbols](http://bl.ocks.org/mbostock/1062383)
* [Hive plot](http://bost.ocks.org/mike/hive/)
* [Collision detection](http://mbostock.github.io/d3/talk/20111018/collision.html)
* [D3.js world map with force layout](http://bl.ocks.org/bycoffe/3230965)
* [Force directed states](http://mbostock.github.io/d3/talk/20111018/force-states.html)
* [Collapsible nodes](http://mbostock.github.io/d3/talk/20111116/force-collapsible.html)
* [Chord diagram](http://bl.ocks.org/mbostock/4062006)
