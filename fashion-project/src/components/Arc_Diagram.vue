<template>
  <div id="my_dataviz">
    <!-- <svg :height="height" :width="width">
      <g class="Arcs" />
    </svg> -->
  </div>
</template>

<script>
import * as d3 from "d3";
import rawData from '../twenty_trends.json'
const margin = 20;

function cleanData(data) {
    let fashionData=data;
    //console.log(fashionData);
    let nodes=[];
    let links=[];
    fashionData.forEach(function (n){
      let nodeId=n.Source;
      let nodeName=n.Name;
      let linkSource=n.Source;
      let linkTarget=n.Target;
      let countVogue=n.Vogue;
    nodes.push({
        id: nodeId,
        name: nodeName,
        n: countVogue,
        grp: 1
    });
     links.push({
         source: linkSource,
         target: linkTarget,
         size: countVogue,
         trend: nodeName
    })   ;
    });
    
    fashionData={nodes, links};
    console.log(fashionData);
    data=fashionData;
    return fashionData
}

export default {
  name: "ArcChart",
  data() {
    return {
      data: cleanData(rawData)
    };
  },
  /*props: {
    data: Array,
    height: Number,
    width: Number,
  },*/
  computed: {
    xScale() {
      return d3
        .scaleBand()
        .padding(0.1)
        .domain(this.data.map((d) => d.name))
        .range([0, this.width]);
    },
    yScale() {
      return d3
        .scaleLinear()
        .domain([
          Math.min(
            0,
            d3.min(this.data, (d) => d.temperature)
          ),
          d3.max(this.data, (d) => d.temperature),
        ])
        .range([0, this.height - margin]);
    },
    rectWidth() {
      return this.xScale.bandwidth();
    },
  },
  mounted() {
    var margin = {top: 0, right: 30, bottom: 50, left: 60},
    width = 1080 - margin.left - margin.right,
    height = 920 - margin.top - margin.bottom;
console.log('here')
    // append the svg object to the body of the page
    var svg = d3.select("#my_dataviz")
    .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
    .append("g")
        .attr("transform",
            "translate(" + margin.left + "," + margin.top + ")");

// Read dummy data
    //let fashionData;
    // d3.json("twenty_trends.json", function(data) {
        
        

        //console.log(data);

    // List of node names
    
    var allNodes = this.data.nodes.map(function(d){return d.id})

    // List of groups
    var allGroups = this.data.links.map(function(d){return d.size})
    allGroups = [...new Set(allGroups)]

    // A color scale for groups:
    var color = d3.scaleOrdinal()
        .domain(allGroups)
        //.range(d3.schemeSet3);

    // A linear scale for node size
    /*var size = d3.scaleLinear()
        .domain([1,10])
        .range([2,10]);*/

    // A linear scale to position the nodes on the X axis
    var x = d3.scalePoint()
        .range([0, width])
        .domain(allNodes.sort(function(a,b) { return a - b }))
        
        console.log(x.domain());

    // In my input data, links are provided between nodes -id-, NOT between node names.
    // So I have to do a link between this id and the name
    /*var idToNode = {};
    data.nodes.forEach(function (n) {
        idToNode[n.id] = n;
    });*/

    // Add the links
    
    var linksDraw = svg
        .selectAll('mylinks')
        .data(this.data.links)
        .enter()
        .append('path')
        .attr('d', function (d) {
        let start = x(d.source)    // X position of start node on the X axis
        let end = x(d.target)      // X position of end node
        //console.log(d.trend, start, end)
        return ['M', start, height-30,    // the arc starts at the coordinate x=start, y=height-30 (where the starting node is)
            'A',                            // This means we're gonna build an elliptical arc
            (start-end)/2, ',',    // Next 2 lines are the coordinates of the inflexion point. Height of this point is proportional with start - end distance
            (start-end)/2*(Math.log(d.size))/3.5, 0, 0, ',', //adding multiple of (d.size*920) to seperate lines based on name and add in count as piece of data 
            start < end ? 1 : 0, end, ',', height-30] // We always want the arc on top. So if end is before start, putting 0 here turn the arc upside down.
            .join(' ');
        })
        .style("fill", 'none')
        .attr("stroke", function (d) { if (d.trend=='bell bottoms') {return '#59C3C3'}
            else if (d.trend=='corduroy') {return '#8D6A9F'}
            else if (d.trend=='cutouts') {return '#FFF21F1'}
            else if (d.trend=='high waisted pants') {return '#B0FF92'}
            else if (d.trend=='low rise jeans') {return '#D6FF79'}
            else if (d.trend=='mini-skirt') {return '#B33F62'}
            else if (d.trend=='off the shoulder') {return '0FA3B1'}
            else if (d.trend=='plaid flannel shirt') {return '##6320EE'}
            else if (d.trend=='shift dress'){return '#F7A072'}
            else if (d.trend=='shoulder pads'){return '#FFF689'}
            else if (d.trend=='tie-dye'){return '#48BEFF'}
            else if (d.trend=='platform shoes'){return '#3DFAFF'}
            else if (d.trend=='leather jacket'){return '#4A306D'}
            else if (d.trend=='sequin'){return '#FF99C9'}
            else if (d.trend=='peplum shirt'){return '#084887'}
            else if (d.trend=='fringe detail'){return 'C0C999'}
            else if (d.trend=='patchwork'){return '#2E5EAA'}
            else if (d.trend=='polka dot'){return '#C3BEF7'}
            else if (d.trend=='balloon sleeves'){return '#B30089'}
            else if (d.trend=='acid wash'){return '#E55934'}
            else {return 'grey'}
        })
        .style("stroke-width", 2.5)

    // Add the circle for the nodes
    var nodesDraw = svg
        .selectAll("mynodes")
        .data(this.data.nodes.sort(function(a,b) { return +b.id - +a.id }))
        .enter()
        .append("circle")
        .attr("cx", function(d){ return(x(d.id))})
        .attr("cy", height-30)
        .attr("r", 10)
        .style("fill", 'white')
        .attr("stroke", "black")
        .style("stroke-width", '2.5')

    // And give them a label
    var labels = svg
        .selectAll("mylabels")
        .data(this.data.nodes)
        .enter()
        .append("text")
        .attr("x", 0)
        .attr("y", 0)
        .text(function(d){ return(d.id)} )
        .style("text-anchor", "end")
        .attr("transform", function(d){ return( "translate(" + (x(d.id)) + "," + (height-15) + ")rotate(-45)")})
        .style("font-size", 24)

    // Add the highlighting functionality
    nodesDraw
        .on('mouseenter', function (d) {
        // Highlight the nodes: every node is green except of him
        nodesDraw
            .style('opacity', .2)
        d3.select(this)
            .style('opacity', 1)
        // Highlight the connections
        linksDraw
            .style('stroke', function (link_d) { return link_d.source === d.id || link_d.target === d.id ? color(d.name) : '#b8b8b8';})
            .style('stroke-opacity', function (link_d) { return link_d.source === d.id || link_d.target === d.id ? 1 : .2;})
            .style('stroke-width', function (link_d) { return link_d.source === d.id || link_d.target === d.id ? 4 : 1;})
        labels
            .style("font-size", function(label_d){ return label_d.name === d.name ? 16 : 2 } )
            .attr("y", function(label_d){ return label_d.name === d.name ? 10 : 0 } )
            .text(function(d){ return(d.id + `-` + d.name)} )

        })
        .on('mouseout', function () {
        nodesDraw.style('opacity', 1)
        linksDraw
            .style("fill", 'none')
            .attr("stroke", function (d) { if (d.trend=='bell bottoms') {return '#59C3C3'}
            else if (d.trend=='corduroy') {return '#8D6A9F'}
            else if (d.trend=='cutouts') {return '#FFF21F1'}
            else if (d.trend=='high waisted pants') {return '#B0FF92'}
            else if (d.trend=='low rise jeans') {return '#D6FF79'}
            else if (d.trend=='mini-skirt') {return '#B33F62'}
            else if (d.trend=='off the shoulder') {return '0FA3B1'}
            else if (d.trend=='plaid flannel shirt') {return '##6320EE'}
            else if (d.trend=='shift dress'){return '#F7A072'}
            else if (d.trend=='shoulder pads'){return '#FFF689'}
            else if (d.trend=='tie-dye'){return '#48BEFF'}
            else if (d.trend=='platform shoes'){return '#3DFAFF'}
            else if (d.trend=='leather jacket'){return '#4A306D'}
            else if (d.trend=='sequin'){return '#FF99C9'}
            else if (d.trend=='peplum shirt'){return '#084887'}
            else if (d.trend=='fringe detail'){return 'C0C999'}
            else if (d.trend=='patchwork'){return '#2E5EAA'}
            else if (d.trend=='polka dot'){return '#C3BEF7'}
            else if (d.trend=='balloon sleeves'){return '#B30089'}
            else if (d.trend=='acid wash'){return '#E55934'}
            else {return 'grey'}
        })
        .style('stroke-width', '2.5') 
        labels 
            .style("font-size", 24 )
            .text(function(d){return d.id})
            .style("text-anchor", "end")
            .attr("transform", function(d){ return( "translate(" + (x(d.id)) + "," + (height-15) + ")rotate(-45)")})

        })
  }
}
// })
</script>

<style>
.slider {
  max-width: 50%;
  margin: 0 auto;
}
input[type="range"]::-webkit-slider-thumb {
  cursor: ew-resize; /* grab */
}
</style>