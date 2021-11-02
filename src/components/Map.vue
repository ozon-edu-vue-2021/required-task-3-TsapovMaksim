<template>
  <div class="map">
    <h3>Карта офиса</h3>

    <div v-if="!isLoading" class="map-root">
      <MapSvg ref="map" />
      <TableSvg ref="table" v-show="false" />
    </div>
    <div v-else>Loading...</div>
  </div>
</template>

<script>
import * as d3 from "d3";

import MapSvg from "../assets/images/map.svg";
import TableSvg from "../assets/images/workPlace.svg";

import tables from "../assets/data/tables.json";
import legends from "../assets/data/legend.json";

export default {
  components: {
    MapSvg,
    TableSvg,
  },
  mounted() {
    this.map = d3.select(this.$refs.map);
    this.group = this.map.select("g");
    this.table = d3.select(this.$refs.table);
    this.tables = tables;

    if (this.group) {
      this.drawTables();
    } else {
      console.log("g not found");
    }
  },

  data() {
    return {
      isLoading: false,
      map: null,
      group: null,
      table: null,
      tables: [],
    };
  },
  methods: {
    drawTables() {
      const svgTablesGroup = this.group.append("g").classed("grupPLaces", true);

      this.tables.forEach((table) => {
        const svgTable = svgTablesGroup
          .append("g")
          .attr("transform", `translate(${table.x} ${table.y}) scale(0.5)`)
          .attr("id", table._id)
          .classed("employer-place", true);

        svgTable
          .append("g")
          .attr("transform", `rotate(${table.rotate || 0})`)
          .attr(
            "fill",
            `${
              legends.find((l) => l.group_id === table.group_id)?.color ??
              "transparent"
            }`
          )
          .html(this.table.html());
      });
    },
  },
};
</script>

<style scoped>
.map {
  height: 100%;
  width: 100%;
  padding: 24px;
  overflow: hidden;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}

.map-root {
  height: 100%;
  width: 100%;
  overflow: hidden;
  box-sizing: border-box;
}

h3 {
  margin-top: 0px;
}

::v-deep svg {
  height: 100%;
  width: 100%;
}

::v-deep .table {
  cursor: pointer;
}
</style>
