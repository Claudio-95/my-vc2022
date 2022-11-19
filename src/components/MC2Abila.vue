<template>
  <b-container>
    <b-row>
      <h1>MC2 - Visual Analytics 2022</h1>
    </b-row>
    <b-row>
      <b-col>
        <b-row>
          <h2>Abila Map</h2>
          <svg id="abila_map" width="650" height="550">
            <g id="abila_building" class="building" ref="building">
              <image id="abila_image" href="/MC2/MC2-tourist.jpg" width="100%" height="100%"></image>
            </g>
            <g class="trajectories" ref="trajectories"></g>
          </svg>
        </b-row>
        <b-row>
          <b-form-input id="timeRange" v-model="timeInterval" type="range"
                        min="0" max="837" step="1"></b-form-input>
          <div>Value: {{timeInterval}}</div>
        </b-row>
      </b-col>
      <b-col cols="3">
        <h2>Persons</h2>
        <b-list-group class="personList">
          <b-list-group-item v-for="p in persons" :key="p.id"
                             :variant="p.selected ? 'warning': ''"
                             class="d-flex justify-content-between align-items-center">
            <b-badge variant="primary" pill>{{p.id}}</b-badge>
            {{p.person}}
          </b-list-group-item>
        </b-list-group>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>

import TrajectoryView from '@/assets/TrajectoryView';

const d3 = require('d3');
const trajectories = TrajectoryView();

// loading csv files
//const car = d3.csv("/MC2/car-assignments.csv");
//const credit_card = d3.csv("/MC2/cc_data.csv");
//const gps = d3.csv("/MC2/gps.csv");
//const loyalty_card = d3.csv("/MC2/loyalty_data.csv");

export default {
  name: "MC2Abila",
  data() {
    return {
      persons: [],
      timeInterval: 10,
    };
  },
  mounted() {
    let svg = d3.select("#abila_map");
    let width = +svg.attr("width");
    let height = +svg.attr("height");
    let abila = d3.json('/MC2/Geospatial/Abila.json');
    abila.then(function(data){
      let center = d3.geoCentroid(data);
      let scale = 150;
      let offset = [width / 2, height / 2];
      let projection = d3.geoMercator()
          .scale(scale)
          .center(center)
          .translate(offset);
      let path = d3.geoPath().projection(projection);
      let bounds = path.bounds(data);
      let hscale = scale * width / (bounds[1][0] - bounds[0][0]);
      let vscale = scale * height / (bounds[1][1] - bounds[0][1]);
      scale = (hscale < vscale) ? hscale : vscale;
      offset = [width - (bounds[0][0] + bounds[1][0]) / 5, height - (bounds[0][1] + bounds[1][1]) / 2.5];
      projection = d3.geoMercator()
          .center(center)
          .scale(scale)
          .translate(offset);
      path = path.projection(projection);
      let g = d3.select("#abila_building");
      g.selectAll("path")
          .data(data.features)
          .enter()
          .append("path")
          .attr("fill", "#ccc")
          .attr("stroke", "#333")
          .attr("d", path);
    });

  },
  watch: {
    timeInterval(newVal) {
      const interval = [Math.max(0, newVal - 10), newVal];
      trajectories.timeExtent(interval);
      d3.select(this.$refs.trajectories)
          .call(trajectories);
    },
  },
}
</script>

<style scoped>
#abila_image {
  opacity: 0.5;
}
</style>
