<template>
  <div class="visualization-component">
    <h1>Resource Projects vs World Bank Visualization</h1>
    <div class="visualization-container">
      <div class="visualization__form-area">
        <h5>Plot variables</h5>
        <div class="input">
          <label for="reportYear">Year</label> 
          <input v-model="reportYear" name="reportYear" placeholder="Report Year">
        </div>
        <div class="input">
          <label for="indicator">World Bank Indicator ID</label> 
          <input v-model="indicator" name="indicator" placeholder="World Bank Indicators">
        </div>
        <div class="input">
          <label for="xColumnName">X-Axis Label</label> 
          <input v-model="xColumnName" name="xColumnName" placeholder="X-axis column name">
        </div>
        <div class="input">
          <label for="yColumnName">Y-Axis Label</label> 
          <input v-model="yColumnName" name="yColumnName" placeholder="Y-axis column name">
        </div>
        <button @click="drawPlot(false)" class="btn">Update Plot</button>
        <div class="visualization__link-area">
          <a href="https://data.worldbank.org/indicator/?tab=all" target="__blank" class="visualization__form-area-link">Search for world bank indicators</a>
        </div>
      </div>
      <div id="visualization__plot-area" ref="plot" class="visualization__plot-area"></div>
    </div>
  </div>
</template>
<script>
import * as d3 from 'd3';
let query = `https://api.apihighways.org/v1/query?sql=SELECT%20country,sum(payment)%20FROM%2021c1039e-8fc6-4aae-8ebb-77c580c63057%20`
let worldBankQuery = `https://api.worldbank.org/v2/countries/all/indicators/`
export default {
  async mounted(){
    query += `where%20reportYear%20=%20${this.reportYear}%20group%20by%20country`
    let response = await this.$axios.$get(query);
    this.xAxisData = response.data;
    worldBankQuery += `${this.indicator}?format=json&per_page=30000&date=${this.reportYear}`;
    let worldBankResponse = await this.$axios.$get(worldBankQuery);
    this.yAxisData = worldBankResponse[1];
    this.data = this.xAxisData.reduce((result,datapoint) => {
      //find data point
      let countryDataPoint = this.yAxisData.find(y => y.country.value.toLowerCase() === datapoint.country.toLowerCase())
      if(countryDataPoint && countryDataPoint.country.value != 'Russian Federation')
        result.push(Object.assign(datapoint,{'yValue':countryDataPoint.value,'payment':datapoint["SUM(payment)"]}))
      else
        console.log(`No datapoint found for ${datapoint.country}`)
      return result;
    }, [])
    this.drawPlot(true)
    // response = await this.$axios.$get(`http://api.worldbank.org/v2/indicators?format=json`);
    // this.indicators = response[1];

  },
  data(){
    return {
      reportYear: 2016,
      dataSetId: '2021c1039e-8fc6-4aae-8ebb-77c580c63057',
      indicator: 'NY.GDP.MINR.RT.ZS',
      // indicators: [],
      data:[],
      xAxisData:[],
      yAxisData:[],
      yColumnName: 'Mineral Rents (% of GDP)',
      xColumnName: 'Resource Payments (in USD)',
      plotWidth: 840,
      plotHeight: 500,
      svg: null
    }
  },
  methods:{
    drawPlot(initial){
      let margin = {top: 20, right: 20, bottom: 60, left: 20},
        width = this.plotWidth - margin.left - margin.right,
        height = this.plotHeight - margin.top - margin.bottom;
      let that = this;
      let x = d3.scaleLinear()
          .range([0, width]);

      let y = d3.scaleLinear()
          .range([height, 0]);

      // var zoom = d3.zoom()
      //   .scaleExtent([1, 40])
      //   .translateExtent([[-100, -100], [width + 90, height + 100]])
      //   .on("zoom", zoomed);


      //let color = d3.schemeCategory10();

      let xAxis = d3.axisBottom(x)

      let yAxis = d3.axisLeft(y)      
      if(this.svg != null)
      {
        this.svg.remove();
        this.$refs.plot.removeChild(this.$refs.plot.children[0]);
      } 
      this.svg = d3.select("#visualization__plot-area")
          .append("svg")
          .attr("width", "100%")
          .attr("height", height + margin.top + margin.bottom)
          .attr("viewBox",`0 0 ${this.plotWidth} ${this.plotHeight}`)
          .attr('preserveAspectRatio', "xMidYMid meet")
          .append("g")
          .attr("transform", `translate(${margin.left},${margin.top})`);


        x.domain(d3.extent(this.data, function(d) { return 1*d.payment;})).nice();
        y.domain(d3.extent(this.data, function(d) { return 1*d.yValue; })).nice();

        this.svg.append("g")
            .attr("class", "x axis")
            .attr("transform", "translate(0," + height + ")")
            .call(xAxis)
          .append("text")
            .attr("class", "label")
            .attr("x", width)
            .attr("y", -6)
            .style("text-anchor", "end")
            .style("fill","black")
            .text(this.xColumnName);

        this.svg.append("g")
            .attr("class", "y axis")
            .call(yAxis)
          .append("text")
            .attr("class", "label")
            .attr("transform", "rotate(-90)")
            .attr("y", 6)
            .attr("dy", ".71em")
            .style("text-anchor", "end")
            .style("fill","black")
            .text(this.yColumnName)

        this.svg.selectAll(".dot")
            .data(this.data)
          .enter().append("circle")
            .attr("class", "dot")
            .attr("r", 3.5)
            .attr("cx", function(d) { return x(d.payment); })
            .attr("cy", function(d) { return y(d.yValue); })
            .style("fill", function(d) { return that.randRGB() })
            .call((selection)=> {
              selection.on('mouseover.tooltip', function (d) {
                const tooltip = that.svg.select('.tooltip');
                const tooltip_title = tooltip
                  .select("tspan#tooltip_title")
                  .text(`${d.country}`);
                tooltip.select('tspan#tooltip-xValue')
                  .text(`${that.xColumnName} : $${d.payment}`);
                tooltip.select('tspan#tooltip-yValue')
                  .text(`${that.yColumnName} : ${d.yValue}%`);
                tooltip
                  .attr('transform', `translate(${[d3.mouse(this)[0] + 10, d3.mouse(this)[1] - 35]})`);
                tooltip.style('display',null)

              })
              .on('mousemove.tooltip', function (d) {
                that.svg.select('.tooltip').style('display', null);
                const new_rect_size = that.svg.select('.tooltip').select('text').node().getBoundingClientRect().width + 20;
                that.svg.select('.tooltip').select('rect')
                  .attr('width', new_rect_size);
              })
              .on('mouseout', function () {
                that.svg.select('.tooltip').style('display', 'none');
              });
            });
        this.prepareTooltip(this.svg);
        // this.svg.call(zoom);
        // function zoomed() {
        //   view.attr("transform", d3.event.transform);
        //   gX.call(xAxis.scale(d3.event.transform.rescaleX(x)));
        //   gY.call(yAxis.scale(d3.event.transform.rescaleY(y)));
        // }
        // let legend = svg.selectAll(".legend")
        //     .data(color.domain())
        //   .enter().append("g")
        //     .attr("class", "legend")
        //     .attr("transform", function(d, i) { return "translate(0," + i * 20 + ")"; });

        // legend.append("rect")
        //     .attr("x", width - 18)
        //     .attr("width", 18)
        //     .attr("height", 18)
        //     .style("fill", color);

        // legend.append("text")
        //     .attr("x", width - 24)
        //     .attr("y", 9)
        //     .attr("dy", ".35em")
        //     .style("text-anchor", "end")
        //     .text(function(d) { return d; });

    },
    randRGB(){
      return d3.rgb(parseInt(Math.random()*255),parseInt(Math.random()*255),parseInt(Math.random()*255));
    },
    prepareTooltip() {
      const tooltip = this.svg.append("g")
        .attr("class", "tooltip")
        .style("display", "none");

      tooltip.append("rect")
        .attr("height", 80)
        .attr("fill", "beige")
        .style("opacity", 0.65);

      let text_zone = tooltip.append("text")
        .attr("x", 10)
        .attr("dy", "0")
        .style('font-family', 'sans-serif')
        .attr("font-size", "11px")
        .style('text-anchor', 'start')
        .style('fill', 'black');

      text_zone.append("tspan")
        .attr("id","tooltip_title")
        .attr("x",10)
        .attr("dy",15)
        .style("font-size","12px")
        .style("font-weight","800");
      text_zone.append("tspan")
        .attr("id","tooltip-xValue")
        .attr("x",10)
        .attr("dy",15)
        .style("font-size","12px")
      text_zone.append("tspan")
        .attr("id","tooltip-yValue")
        .attr("x",10)
        .attr("dy",15)
        .style("font-size","12px")
    }
  } 

}
</script>
<style lang="scss" scoped>
  svg {
    width: 100%
  }
  h1{
    color: #435198;
    font-size: 36px;
    margin-bottom: 20px;
  }
  //form css
  input,select{
    width: 100%;
    height: 40px;
    padding: 0 32px;
    padding-right: 80px;
    border-radius: 2px 2px 0 0;
    border: 0;
    outline: none;
    font-size: 1rem;
    border: solid 1px rgba(69, 69, 69, 0.5);
    display: table;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    margin-bottom: 20px;  }
  .btn{
    display: block;
    color: #FFFFFF;
    background-color: #435198;
    border: solid 1px #FFFFFF;
    font-size: 0.875rem;
    text-transform: uppercase;
    text-align: center;
    padding: 22px;
    border-radius: 2px;
    outline: none;
    cursor: pointer;
  }
  .visualization-component{
    display: block;
    width: 100%;
  }
  .visualization-container{
    display: flex;  
  }
  .visualization{
    &__form-area{
      width: 40%;
      padding: 40px;
      text-align: left;
      label{
        font-size: 10px;
        font-weight: bold;
      }
    }
    &__plot-area{
      width: 60%;
    }
    &__link-area{
      text-align: left;
      margin-top: 40px;
      a{
        text-decoration: none;
        color: #435198;
      }
    }
  }
  //svg css
  .axis path,
  .axis line {
    fill: none;
    stroke: #000;
    shape-rendering: crispEdges;
  }

  .dot {
    stroke: #000;
  }
</style>
