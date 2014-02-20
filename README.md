CS171 - lab 4 - layouts
====

### Introduction

* [Lecture 4](http://cm.dce.harvard.edu/2014/02/24028/L04/screen_H264MultipleHighLTH-16x9.shtml) on visual variables
* [Lecture 6](http://cm.dce.harvard.edu/2014/02/24028/L06/screen_H264MultipleHighLTH-16x9.shtml) on graphs
* D3's [documentation](https://github.com/mbostock/d3/wiki/Layouts) on layouts

Keep in mind that a layout drawing with D3 can be divided into those two parts:

* Layout *data* generation
* `SVG` element drawing (often a `<path>` element)

### Force-directed layout

* Some layouts examples have been [presented](http://bl.ocks.org/mbostock/4062045) during the class 
* [Documentation](https://github.com/mbostock/d3/wiki/Force-Layout)

Let start with [Homework 2](https://github.com/CS171/HW2)
* Look at the variations

**Adding more nodes**

* Create new nodes for each mouse moves at the [pointer position](https://github.com/mbostock/d3/wiki/Selections#wiki-d3_mouse)

**The `.tick()` function**

* Computes the velocity decay and position; can be manually triggered
* Run in the console `force.start();force.tick();force.stop();`
* Test a `.tick()` loop at the init of the page to pre-generate the layout

**Layout division into foci**

* `.tick()` can also be overrided for custom layouts
* Muliti-foci layout


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

* [Hive plot](http://bost.ocks.org/mike/hive/)
* [Collision detection](http://mbostock.github.io/d3/talk/20111018/collision.html)
* [D3.js world map with force layout](http://bl.ocks.org/bycoffe/3230965)
* [Force directed states](http://mbostock.github.io/d3/talk/20111018/force-states.html)
* [Collapsible nodes](http://mbostock.github.io/d3/talk/20111116/force-collapsible.html)
