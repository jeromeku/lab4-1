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

### Walkthrough D3 layouts


**Understanding `SVG` shape drawing functions**

### Tree layout

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
