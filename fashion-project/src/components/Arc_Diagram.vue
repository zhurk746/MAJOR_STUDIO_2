<template>
  <div>
    <h1>Fashion and its History: Does it repeat itself?</h1>
    <h3>
      A visual analysis of Vogue fashion trend data over the last 130 years.
    </h3>
    <p>
      Begin your exploration of fashion trends over the last two centuries by
      clicking each node within the color legend to redraw the visualization
      below. Height of links between each decade is representative of the number
      of times a search term appeared in the Vogue archives during the given
      time period. Fashion trends explored were chosen based on inspiration from
      various different exhibits along with peer feedback.
    </p>
    <p>
      Split up the fashion trends in several categories to guide your
      exploration. Begin by examining trends for bottoms and take a look at high
      rise vs low rise jeans. Next take a look at various sleeve trends such as,
      balloon sleeves, cut-outs and off the shoulder. Hover over the varying
      decades to get a better understanding of the time period when particular
      trends were popular. Feel free to interact with and rebuild the piece in a way that caters to your own fashion interests!
    </p>
    <div class="flex-container">
      <div
        id="my_colorlegend"
        :key="filterLegend.length"
        class="flex-child"
      ></div>
      <div id="my_dataviz" class="flex-child"></div>
    </div>
    <!-- <svg :height="height" :width="width">
      <g class="Arcs" />
    </svg> -->
  </div>
</template>

<script>
import * as d3 from "d3";
import rawData from "../twenty_trends.json";
// const margin = 20;

function cleanData(data) {
  let fashionData = data;
  //console.log(fashionData);
  let nodes = [];
  let links = [];
  fashionData.forEach(function (n) {
    let nodeId = n.Source;
    let nodeName = n.Name;
    let linkSource = n.Source;
    let linkTarget = n.Target;
    let countVogue = n.Vogue;
    nodes.push({
      id: nodeId,
      //name: nodeName,
      //n: countVogue
    });
    links.push({
      source: linkSource,
      target: linkTarget,
      size: countVogue,
      trend: nodeName,
    });
  });
  const uniqueNodes = [...new Set(nodes.map((d) => d.id))];
  fashionData = {
    nodes: uniqueNodes,
    links: links,
  };
  console.log(fashionData);
  data = fashionData;
  return fashionData;
}

const colorLookup = {
  "bell bottoms": "#59C3C3",
  corduroy: "#8D6A9F",
  cutouts: "#FF21F1",
  "high waisted pants": "#3A86FF",
  "low rise jeans": "#D6FF79",
  "mini-skirt": "#B33F62",
  "off the shoulder": "#0FA3B1",
  "plaid flannel shirt": "#6320EE",
  "shift dress": "#F7A072",
  "shoulder pads": "#F2E34C",
  "tie-dye": "#48BEFF",
  "platform shoes": "#3DFAFF",
  "leather jacket": "#4A306D",
  sequin: "#FF99C9",
  "peplum shirt": "#084887",
  "fringe detail": "#C0C999",
  patchwork: "#2E5EAA",
  "polka dot": "#C3BEF7",
  "balloon sleeves": "#B30089",
  "acid wash": "#E55934",
};

const decadeDetail = {
  1890: ["We begin our exploration of fashion", "trends in the late 1800s where we can see references", "of old timey styles like corduroy and balloon sleeves"],
  1900: ["We first see polka-dots", "bell bottoms and shoulder pads gain popularity", "in the early 20th century"],
  1910: ["The peplum style of top first gains", "popularity in the 1910s with a narrower silhouette and", "broader fit around the shoulders"],
  1920: ["This was a time known for glamour and ornate details.", "Cutouts became more popular during this time period", "as more intricate detail was added to garments."],
  1930: ["Leather jackets are the style symbol of the era", "as society begins to drawn fashion", "inspiration from film stars and celebrities"],
  1940: ["Fabric shortages from WW2 played a huge", "role in the recycling of styles and a", "shift to using lesser fabric in the 40s"],
  1950: ["The post war era marked a return to ultra", "feminine silhouettes with polka", "dots and higher waisted bottoms."],
  1960: ["Miniskirts first came into style", "in the late 50s and 60s as a symbol for", "the societal shift occuring with the youth movement."],
  1970: ["The 70's was a decade where tie-dye,", "bell-bottoms and fringe were popular reminiscent of the", "societal movements and youth culture of the era"],
  1980: ["Shoulder pads became symbolic for", "women in the workplace demanding equality", "and power in their positions"],
  1990: ["Platform shoes, specifically sneakers", "gain popularity and sneaker styles symbolize", "the hip-hop culture of the 90s era"],
  2000: ["Low rise jeans and acid wash", "become the grunge symbols of the Y2K era."],
  2010: ["Skinny jeans were popular through", "the early 2000s and 2010s. Looser flare jeans", "have made a comeback in the current decade."],
  2020: ["The Y2K era is back."]
};

function legendDraw(onLegendClick, uniqueTrends) {
  var color_legend = d3
    .select("#my_colorlegend")
    .append("svg")
    .attr("width", 250)
    .attr("height", 550)
    .append("g")
    .attr("transform", "translate(0, 20)");

  const titlePadding = 20; // padding between title and entries
  const entrySpacing = 25; // spacing between legend entries
  const entryRadius = 8; // radius of legend entry marks
  const labelOffset = 8; // additional horizontal offset of text labels
  const baselineOffset = 4; // text baseline offset, depends on radius and font size

  color_legend
    .append("text")
    .attr("x", 0)
    .attr("y", 0)
    .attr("fill", "black")
    .attr("font-family", "Helvetica Neue, Arial")
    .attr("font-weight", "bold")
    .attr("font-size", 20)
    .text("Trends");

  //console.log(this.data.links);
  const entries = color_legend
    .selectAll("g")
    .data(uniqueTrends)
    .join("g")
    .attr("transform", function (d, i) {
      return `translate(0, ${titlePadding + i * entrySpacing})`;
    });

  console.log(entries);

  entries
    .append("circle")
    .attr("cx", entryRadius) // <-- offset symbol x-position by radius
    .attr("r", entryRadius)
    .attr("fill", function (d) {
      return colorLookup[d] || "grey";
      /*if (d=='bell bottoms') {return '#59C3C3'}
            else if (d=='corduroy') {return '#8D6A9F'}
            else if (d=='cutouts') {return '#FFF21F1'}
            else if (d=='high waisted pants') {return '#B0FF92'}
            else if (d=='low rise jeans') {return '#D6FF79'}
            else if (d=='mini-skirt') {return '#B33F62'}
            else if (d=='off the shoulder') {return '0FA3B1'}
            else if (d=='plaid flannel shirt') {return '##6320EE'}
            else if (d=='shift dress'){return '#F7A072'}
            else if (d=='shoulder pads'){return '#FFF689'}
            else if (d=='tie-dye'){return '#48BEFF'}
            else if (d=='platform shoes'){return '#3DFAFF'}
            else if (d=='leather jacket'){return '#4A306D'}
            else if (d=='sequin'){return '#FF99C9'}
            else if (d=='peplum shirt'){return '#084887'}
            else if (d=='fringe detail'){return 'C0C999'}
            else if (d=='patchwork'){return '#2E5EAA'}
            else if (d=='polka dot'){return '#C3BEF7'}
            else if (d=='balloon sleeves'){return '#B30089'}
            else if (d=='acid wash'){return '#E55934'}
            else {return 'grey'}*/
    })
    .on("click", function (event, d) {
      onLegendClick(d);
    });

  entries
    .append("text")
    .attr("x", 3 * entryRadius + labelOffset) // <-- place labels to the left of symbols
    .attr("y", baselineOffset) // <-- adjust label y-position for proper alignment
    .attr("fill", "black")
    .attr("font-family", "Helvetica Neue, Arial")
    .attr("font-size", 16)
    .style("user-select", "none") // <-- disallow selectable text
    .text(function (d) {
      return d;
    });
}

export default {
  name: "ArcChart",
  data() {
    return {
      data: cleanData(rawData),
      hoveredNode: null,
      filterLegend: [],
    };
  },
  methods: {
    onmouseover(d) {
      console.log(d);
      this.hoveredNode = d.id;
      //this.width = Math.min(MAX_SVG_WIDTH, window.innerWidth);
    },
    onLegendClick(trend) {
      // console.log(this.filterLegend)
      this.filterLegend = [...this.filterLegend, trend];
      // console.log(this.filterLegend)
    },
  },
  /*props: {
    data: Array,
    height: Number,
    width: Number,
  },*/
  computed: {
    uniqueTrends() {
      return [...new Set(this.data.links.map((d) => d.trend))];
    },
    // xScale() {
    //   return d3
    //     .scaleBand()
    //     .padding(0.1)
    //     .domain(this.data.map((d) => d.name))
    //     .range([0, this.width]);
    // },
    // yScale() {
    //   return d3
    //     .scaleLinear()
    //     .domain([
    //       Math.min(
    //         0,
    //         d3.min(this.data, (d) => d.temperature)
    //       ),
    //       d3.max(this.data, (d) => d.temperature),
    //     ])
    //     .range([0, this.height - margin]);
    // },
    // rectWidth() {
    //   return this.xScale.bandwidth();
    // },
  },
  mounted() {
    legendDraw(this.onLegendClick, this.uniqueTrends);
    const that = this;
    var margin = { top: 0, right: 15, bottom: 50, left: 17 },
      width = 2560 - margin.left - margin.right,
      height = 920 - margin.top - margin.bottom;
    console.log("here");
    // append the svg object to the body of the page

    var svg = d3
      .select("#my_dataviz")
      .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

    // Read dummy data
    //let fashionData;
    // d3.json("twenty_trends.json", function(data) {

    //console.log(data);

    // List of node names

    /*var allNodes = this.data.nodes.map(function (d) {
      return d.id;
    });*/

    // List of groups
    //var allGroups = this.data.links.map(function(d){return d.size})
    //allGroups = [...new Set(allGroups)]

    // A color scale for groups:
    /*var color = d3.scaleOrdinal()
        .domain(allGroups)*/
    //.range(d3.schemeSet3);

    // A linear scale for node size
    /*var size = d3.scaleLinear()
        .domain([1,10])
        .range([2,10]);*/

    // A linear scale to position the nodes on the X axis
    var x = d3
      .scalePoint()
      .range([0, width])
      .domain(
        this.data.nodes.sort(function (a, b) {
          return a - b;
        })
      );

    console.log(x.domain());

    // In my input data, links are provided between nodes -id-, NOT between node names.
    // So I have to do a link between this id and the name
    /*var idToNode = {};
    data.nodes.forEach(function (n) {
        idToNode[n.id] = n;
    });*/

    // Add the links

    var linksDraw = svg
      .selectAll(".mylinks")
      .data(this.data.links)
      .enter()
      .append("path")
      .attr("class", "mylinks")
      .attr("d", function (d) {
        let start = x(d.source); // X position of start node on the X axis
        let end = x(d.target); // X position of end node
        //console.log(d.trend, start, end)
        return [
          "M",
          start,
          height - 30, // the arc starts at the coordinate x=start, y=height-30 (where the starting node is)
          "A", // This means we're gonna build an elliptical arc
          (start - end) / 2,
          ",", // Next 2 lines are the coordinates of the inflexion point. Height of this point is proportional with start - end distance
          ((start - end) / 2) * Math.log(d.size * 5.5),
          0,
          0,
          ",", //adding multiple of (d.size*920) to seperate lines based on name and add in count as piece of data
          start < end ? 1 : 0,
          end,
          ",",
          height - 30,
        ] // We always want the arc on top. So if end is before start, putting 0 here turn the arc upside down.
          .join(" ");
      })
      .style("fill", "none")
      .attr("stroke", function (d) {
        return colorLookup[d.trend] || "grey";
        /*if (d.trend=='bell bottoms') {return '#59C3C3'}
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
            else {return 'grey'}*/
      })
      .style("stroke-width", 2.5);

    console.log(this.data.nodes);
    // Add the circle for the nodes
    var nodesDraw = svg
      .selectAll(".mynodes")
      .data(
        this.data.nodes.sort(function (a, b) {
          return +b - +a;
        })
      )
      .enter()
      .append("circle")
      .attr("class", "mynodes")
      .attr("cx", function (d) {
        return x(d);
      })
      .attr("cy", height - 30)
      .attr("r", 10)
      .attr("fill", "white")
      .attr("stroke", "black")
      .style("stroke-width", "2.5");

    // And give them a label
    var labels = svg
      .selectAll(".mylabels")
      .data(this.data.nodes)
      .join((enter) => {
        const g = enter
          .append("g")
          .attr("class", "mylabels")
          .attr("transform", function (d) {
            return "translate(" + x(d) + "," + (height - 15) + ")rotate(0)";
          });
        g.append("rect").attr("width", 100).attr("height", 200).style("stroke", "5px").style("fill", "none");
        g.append("text").attr("class","decade_Detail")
          .attr("width", 100)
          .attr("height", 200)
          .attr("opacity", 0)
          .attr("x", -100)
          .attr("y", 10)
          .selectAll("tspan").data(function(d) {return decadeDetail[d]}).join("tspan").text(e => e).attr("dy", 15).attr("x",-30)
        g.append("text")
          .attr("class", "decade_Id")
          .attr("x", 40)
          .attr("y", 20)
          .text(function (d) {
            return d;
          })
          .style("text-anchor", "end")
          .style("font-size", 20);
          return g
      });

    //adding a legend

    // <-- our legend helper is invoked just like an axis generator
    nodesDraw
      .on("mouseenter", function (event, d) {
        that.onmouseover(d);
        // Highlight the nodes: every node is green except of him
        nodesDraw.style("opacity", 0.2);
        d3.select(this).style("opacity", 1);
        // Highlight the connections
        linksDraw
          .style("stroke", function (link_d) {
            return link_d.source === d || link_d.target === d
              ? d.trend
              : "#b8b8b8";
          })
          .style("stroke-opacity", function (link_d) {
            return link_d.source === d || link_d.target === d ? 1 : 0.2;
          })
          .style("stroke-width", function (link_d) {
            return link_d.source === d || link_d.target === d ? 4 : 1;
          });
        labels.selectAll(".decade_Id")
          .style("font-size", function (label_d) {
            return label_d === d ? 16 : 16;
          })
          .attr("y", function (label_d) {
            return label_d === d ? 10 : 0;
          })
          .text(function (d) {
            return d;
          });
      labels.selectAll(".decade_Detail").attr("opacity", function (label_d) {
      return label_d === d ? 1 : 0;
      })
      })
      .on("mouseout", function () {
        nodesDraw.style("opacity", 1);
        linksDraw
          .style("fill", "none")
          .attr("stroke", function (d) {
            return colorLookup[d.trend] || "grey";
          })
          .style("stroke-width", "2.5");
          labels.selectAll(".decade_Detail").attr("opacity",0)
        /*labels
          /*.selectAll(".mylabels")
          .data(this.data.nodes)
          .append("rect")
          .style("fill", "none")
          //.html('<div style="width: 150px;">' function (d) {return decadeDetail[d]}'</div>')
          .join((enter) => {
            const g = enter.append("g").attr("class", "mylabels");
            g.append("rect").attr("width", 100).attr("height", 200);
            g.append("text")
              .attr("width", 100)
              .attr("height", 200)
              .text(function (d) {
                return decadeDetail[d];
              })
              .style("font-size", 12)
              .style("text-anchor", "end")
              .attr("transform", function (d) {
                return "translate(" + x(d) + "," + (height - 15) + ")rotate(0)";
              });
            //return g;
          });*/
        /*.text(function (d) {
            return decadeDetail[d];
          })*/

        //.attr("x", 100)
        //.attry("y", 500);
      });
  },
  updated() {
    legendDraw(this.onLegendClick, this.uniqueTrends);
    const that = this;

    var svg = d3.select("#my_dataviz").select("svg").select("g");
    console.log(this.filterLegend);
    svg.selectAll(".mynodes").style("opacity", function (d) {
      if (that.filterLegend.length === 0) {
        return 1;
      }
      console.log(d);
      return that.filterLegend.indexOf(d.name) === -1 ? 0 : 1;
    });

    // Highlight the nodes: every node is green except of him
    //nodesDraw
    // .style('opacity', .2)
    //d3.select(this)

    svg
      .selectAll(".mylinks")
      .attr("stroke", function (d) {
        if (that.filterLegend.indexOf(d.trend) !== -1) {
          return colorLookup[d.trend];
        } else {
          return "D3D3D3";
        }
        //.style('opacity', 1)
        // Highlight the connections
        // const linksDraw = svg
        // .selectAll(".mylinks")
        /*.style('stroke', function (link_d) { return link_d.source === d.id || link_d.target === d.id ? d.name : '#b8b8b8';})
            .style('stroke-opacity', function (link_d) { return link_d.source === d.id || link_d.target === d.id ? 1 : .2;})
            .style('stroke-width', function (link_d) { return link_d.source === d.id || link_d.target === d.id ? 4 : 1;})*/
        // const labels =svg
        // .selectAll(".mylabels")
        /* .style("font-size", function(label_d){ return label_d.name === d.name ? 16 : 2 } )
            .attr("y", function(label_d){ return label_d.name === d.name ? 10 : 0 } )
            .text(function(d){ return(d.id)} )*/
      })
      .attr("stroke-width", "10");

    d3.select("#my_colorlegend")
      .select("svg")
      .selectAll("g")
      .select("circle")
      .attr("stroke", function (d) {
        console.log(d);
        if (that.filterLegend.indexOf(d) !== -1) {
          return "black";
        } else {
          return "white";
        }
      });

    /* svg
      .selectAll(".mylabels")
      .data(this.data.nodes)
      .append("rect")
          .style("fill", "none")
      //.html('<div style="width: 150px;">' function (d) {return decadeDetail[d]}'</div>')
      .join((enter) => {
        const g = enter.append("g").attr("class", "mylabels");
        g.append("rect").attr("width", 100).attr("height", 200);
        g.append("text")
          .attr("width", 100)
          .attr("height", 200)
          .text(function (d) {
            return decadeDetail[d.id];
          })
          .style("font-size", 12)
          .style("text-anchor", "end");
        return g;
      });
    //.style("stroke-width", "3.5");*/
  },
};
</script>

<style>
.slider {
  max-width: 50%;
  margin: 0 auto;
}
input[type="range"]::-webkit-slider-thumb {
  cursor: ew-resize; /* grab */
}
p {
  margin-left: 200px;
  margin-right: 200px;
}
.flex-container {
  /* display: flex; */
  display: grid;
  grid-template-columns: 1fr 3fr;
}
/* 
.flex-child {
  flex: 1;
}

.flex-child:first-child {
  margin-right: 20px;
} */

#my_colorlegend {
  /* width: 25%; */
  margin-top: 200px;
}
#my_dataviz {
  /* width: 75%; */
  overflow-x: scroll;
}

.decade_Detail {
  pointer-events: none;
}
</style>