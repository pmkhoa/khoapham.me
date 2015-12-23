---
date: 2015-12-23T12:00:31-08:00
draft: true
title: Rickshaw Chart Hovering Multiple Values
tags: [Guide, Javascript]
---

[D3](http://d3js.org/) is a great javascript library for creating data visualization chart. I have
been a big fan of it for over a year now. However, whenever I need to develop a
quick chart prototype, I use an alternative library called
[Rickshaw](http://code.shutterstock.com/rickshaw/examples/). It's an
extension from D3, so you can get greatness from both of them.
<!--more-->

One thing that I was challenging me recently that I needed to create a chart
when user hovered on it, it would reveal all y-values at the cursor position.

<div id="chart_container">
	<div id="chart"></div><br>
	<div id="legend"></div>
</div>


<style>
#chart_container {
	width: 600px;
    max-width: 100%;
}
.swatch {
	display: inline-block;
	width: 10px;
	height: 10px;
	margin: 0 8px 0 0;
}
.label {
	display: inline-block;
}
#legend {
	text-align: center;
}
.rickshaw_graph .detail {
	background: none;
}
.line-wrapper {
    opacity: 1;
    background: #f3f7f8;
    padding: 7px 10px;
    border-radius: 3px;
    font-size: 0.8em;
    white-space: nowrap;
    left: 10px;
    position: absolute;
    min-width: 300px;
}
.line-wrapper .line {
    position: relative;
    margin-top: 3px;
}
.line-wrapper .swatch {
    border-radius: 10px;
    width: 9px;
    height: 9px;
    position: absolute;
    top: 3px;
}
.line-wrapper .label {
    font-weight: 600;
    padding-left: 18px;
}
.line-wrapper .label   span {
    font-weight: 400;
}
.line-wrapper .label .metric-legend-label {
    margin-right: 5px;
    padding-right: 5px;
}
.line-wrapper .label .metric-legend-label .text {
    font-weight: 600;
    text-transform: capitalize;
}
</style>

<link rel="stylesheet" href="/blog-vendor/rickshaw.min.css">
<script src="/blog-vendor/d3.min.js"></script>
<script src="/blog-vendor/rickshaw.min.js"></script>

<script type="text/javascript">

    // set up our data series with 50 random data points

    var seriesData = [ [], [], [] ];
    var random = new Rickshaw.Fixtures.RandomData(150);

    for (var i = 0; i < 150; i++) {
        random.addData(seriesData);
    }

    // instantiate our graph!

    var graph = new Rickshaw.Graph( {
        element: document.getElementById("chart"),
        width: 600,
        height: 300,
        renderer: 'line',
        series: [
            {
                color: "#c05020",
                data: seriesData[0],
                name: 'New York'
            }, {
                color: "#30c020",
                data: seriesData[1],
                name: 'London'
            }, {
                color: "#6060c0",
                data: seriesData[2],
                name: 'Tokyo'
            }
        ]
    } );

    graph.render();

    var Hover = Rickshaw.Class.create(Rickshaw.Graph.HoverDetail, {

        render: function(args) {

            var lines = document.createElement('div');
            lines.className = 'line-wrapper';
            lines.innerHTML = args.formattedXValue;
            lines.style.top = args.mouseY + "px";
            this.element.appendChild(lines);
            var boundingRect = this.element.parentNode.getBoundingClientRect();
            if ( args.mouseX > ( boundingRect.width * 2 / 3 ) ) {
                this.element.classList.remove('left');
                this.element.classList.add('right');
            } else {
                this.element.classList.remove('right');
                this.element.classList.add('left');
            }

            args.detail.sort(function(a, b) { return a.order - b.order }).forEach( function(d) {

                var line = document.createElement('div');
                line.className = 'line';

                var swatch = document.createElement('div');
                swatch.className = 'swatch';
                swatch.style.backgroundColor = d.series.color;

                var label = document.createElement('div');
                label.className = 'label';
                label.innerHTML = d.name + ": "+ d.formattedYValue;

                line.appendChild(swatch);
                line.appendChild(label);

                lines.appendChild(line);

                var dot = document.createElement('div');
                dot.className = 'dot';
                dot.style.top = graph.y(d.value.y0 + d.value.y) + 'px';
                dot.style.borderColor = d.series.color;

                this.element.appendChild(dot);

                dot.className = 'dot active';

                // Assume left alignment until the element has been displayed and
                // bounding box calculations are possible.

                this.show();

            }, this );
        }
    });

    var hover = new Hover( { graph: graph } ); 

</script>

The source code for this was simple, as seen below:

    <div id="chart_container">
        <div id="chart"></div><br>
        <div id="legend"></div>
    </div>

<!--break-->


    // set up our data series with 50 random data points

    var seriesData = [ [], [], [] ];
    var random = new Rickshaw.Fixtures.RandomData(150);

    for (var i = 0; i < 150; i++) {
        random.addData(seriesData);
    }

    // instantiate our graph!

    var graph = new Rickshaw.Graph( {
        element: document.getElementById("chart"),
        width: 600,
        height: 300,
        renderer: 'line',
        series: [
            {
                color: "#c05020",
                data: seriesData[0],
                name: 'New York'
            }, {
                color: "#30c020",
                data: seriesData[1],
                name: 'London'
            }, {
                color: "#6060c0",
                data: seriesData[2],
                name: 'Tokyo'
            }
        ]
    } );

    graph.render();
    var Hover = Rickshaw.Class.create(Rickshaw.Graph.HoverDetail, {
        render: function(args) {
            var lines = document.createElement('div');
            lines.className = 'line-wrapper';
            lines.innerHTML = args.formattedXValue;
            lines.style.top = args.mouseY + "px";
            this.element.appendChild(lines);
            var boundingRect = this.element.parentNode.getBoundingClientRect();
            if ( args.mouseX > ( boundingRect.width * 2 / 3 ) ) {
                this.element.classList.remove('left');
                this.element.classList.add('right');
            } else {
                this.element.classList.remove('right');
                this.element.classList.add('left');
            }

            args.detail.sort(function(a, b) { return a.order - b.order }).forEach( function(d) {

                var line = document.createElement('div');
                line.className = 'line';

                var swatch = document.createElement('div');
                swatch.className = 'swatch';
                swatch.style.backgroundColor = d.series.color;

                var label = document.createElement('div');
                label.className = 'label';
                label.innerHTML = d.name + ": "+ d.formattedYValue;

                line.appendChild(swatch);
                line.appendChild(label);

                lines.appendChild(line);

                var dot = document.createElement('div');
                dot.className = 'dot';
                dot.style.top = graph.y(d.value.y0 + d.value.y) + 'px';
                dot.style.borderColor = d.series.color;

                this.element.appendChild(dot);

                dot.className = 'dot active';

                // Assume left alignment until the element has been displayed and
                // bounding box calculations are possible.

                this.show();

            }, this );
        }
    });

    var hover = new Hover( { graph: graph } ); 

