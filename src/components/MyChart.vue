<template>
  <div>
    <div class="chart_container"></div>
  </div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "MyChart",
  data() {
    return {
      w: 1500,
      h: 460,
      pathid: 0,
      margin: {
        top: 20,
        right: 20,
        bottom: 30,
        left: 40
      },
      pathColors: [
        "chartreuse",
        "red",
        "darkblue",
        "cyan",
        "yellow",
        "hotpink",
        "darkorange",
        "purple"
      ],
      colorIndex: 0,
      xList: [],
      visibleKeyList: ["1"],
      jk: ""
    };
  },
  computed: {
    computedActions() {
      return Object.keys(this.actions).map(key => {
        return {
          key: key,
          value: this.actions[key]
        };
      });
    },
    yScale() {
      return d3
        .scaleBand()
        .domain(this.computedActions.map(d => d.value))
        .rangeRound([-20, this.h]);
    }
  },
  methods: {
    nextPathId() {
      this.pathid++;
      console.log(this.pathid);
      return "path" + this.pathid;
    },
    xListComputed(v) {
      return this.xList.push(v);
    },
    generatePath(pathY_points) {
      let nextX = 0;
      let previousY;
      this.xListComputed(nextX);
      const pathPointsArray = [];
      const a = pathY_points.map(element => parseInt(element) * 40);
      a.forEach((y, i) => {
        if (i === 0) {
          previousY = y;
        }
        if (y !== previousY) {
          pathPointsArray.push(nextX - 20);
          pathPointsArray.push(y);
        }
        pathPointsArray.push(nextX);
        pathPointsArray.push(y);
        previousY = y;
        nextX += 20;
        this.xListComputed(nextX);
        pathPointsArray.push(nextX);
        pathPointsArray.push(y);
        nextX += 20;
        this.xListComputed(nextX);
      });
      let path = "";
      pathPointsArray.forEach((point, index) => {
        if (index === 0) {
          path += "M";
          path += point;
          path += " ";
        } else if (index === 1) {
          path += point;
          path += " ";
        } else {
          if (index % 2 === 0) {
            path += "L";
            path += point;
            path += " ";
          } else {
            path += point;
            path += " ";
          }
        }
      });

      return path;
    },
    getPathColor(key) {
      const color = this.pathColors[key];
      return color;
    },

    drawChart() {
      console.log(this.data);
      d3.select("svg").remove();
      const svg = d3
        .select(".chart_container")
        .append("svg")
        .attr("width", this.w)
        .attr("height", this.h);
      //   svg.selectAll("*").remove();
      const g = svg
        .append("g")
        .attr("transform", "translate(" + 200 + "," + 10 + ")")
        .call(d3.axisLeft(this.yScale));

      g.selectAll("line-x")
        .data(this.data)
        .enter()
        .append("path")
        .attr("id", data => this.nextPathId())
        .attr("d", data => this.generatePath(data.value))
        .on("mouseover", (d, i, n) => {
          d3.select("#" + n[i].id).style("stroke-width", 6);

          svg.selectAll("path").sort(function(a, b) {
            // select the parent and sort the path's
            if (a !== null && a.id != n[i].id) return -1;
            // a is not the hovered element, send "a" to the back
            else if (a != null) return 1; // a is the hovered element, bring "a" to the front
          });

          const pathsArray = [...svg.selectAll("path")._groups[0]];
          console.log(pathsArray);

          const index = pathsArray.findIndex(p => {
            return p.id === n[i].id;
          });
        })
        .on("mouseout", (d, i, n) => {
          d3.select("#" + n[i].id).style("stroke-width", 4);
        })
        .style("fill", "none")
        .style("stroke", data => this.getPathColor(data.key))
        .style("stroke-width", 4);

      g.selectAll("line-y")
        .data(this.xList)
        .enter()
        .append("line")
        .attr("stroke-dasharray", "5,5")
        .style("stroke", "gray")
        .attr("x1", d => d)
        .attr("y1", -10)
        .attr("x2", d => d)
        .attr("y2", this.h);
    }
  },
  mounted() {
    this.drawChart();
  },
  updated() {
    this.drawChart();
  },
  watch: {
    data() {
      this.drawChart();
    }
  },
  props: ["data", "actions"]
};
</script>


<style scoped>
.dummy {
}
</style>
