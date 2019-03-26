<template>
  <div>
    <div id="app2">
      <MyChart :actions="actions" :data="data"></MyChart>
    </div>

    <div class="filter-options-container">
      <div v-for="actor in actors" v-bind:key="actor.key" class="filter-options">
        <label
          :id="actor.key"
          :for="actor.key"
          @mouseover="mouseOverLabel($event)"
          @mouseleave="mouseOutLabel($event)"
        >{{actor.value}}</label>
        <input
          type="checkbox"
          :name="actor.value"
          :value="actor.key"
          :id="actor.key"
          @change="inputChanged($event)"
          @mouseover="mouseOver($event)"
          @mouseleave="mouseOut($event)"
        >
      </div>
    </div>
  </div>
</template>

<script>
import MyChart from "./MyChart";
export default {
  name: "Chart",
  components: {
    MyChart
  },
  data() {
    return {
      selectedKeyList: ["1", "2", "3", "4", "5", "6", "7"]
    };
  },
  computed: {
    actions() {
      return this.jsonData["Actions_map"];
    },
    data() {
      return Object.keys(this.jsonData["Adaptation_plans"]["POPSWAT_0"])
        .map(key => {
          return {
            key: key,
            value: this.jsonData["Adaptation_plans"]["POPSWAT_0"][key]
          };
        })
        .filter(d => {
          if (this.selectedKeyList.findIndex(k => k === d.key) > -1) {
            return true;
          } else {
            return false;
          }
        });
    },
    actors() {
      return Object.keys(this.jsonData["Actors_map"]).map(key => {
        return {
          key: parseInt(key, 10) + 1,
          value: this.jsonData["Actors_map"][key]
        };
      });
    }
  },
  methods: {
    inputChanged(event) {
      if (event.target.checked) {
        this.selectedKeyList.push(event.target.value);
      } else {
        const index = this.selectedKeyList.findIndex(
          k => k === event.target.value
        );
        this.selectedKeyList.splice(index, 1);
      }
    },
    mouseOver(event) {
      this.selectedKeyList = [];
      const highlightPathKey = event.target.value;
      const index = this.selectedKeyList.findIndex(
        k => k === event.target.value
      );
      this.selectedKeyList.splice(index, 1);
      this.selectedKeyList.push(highlightPathKey);
    },
    mouseOut() {
      this.selectedKeyList = ["1", "2", "3", "4", "5", "6", "7"];
    },
    mouseOverLabel(event) {
      this.selectedKeyList = [];
      const highlightPathKey = event.target.id;
      const index = this.selectedKeyList.findIndex(
        k => k === event.target.value
      );
      this.selectedKeyList.splice(index, 1);
      this.selectedKeyList.push(highlightPathKey);
    },
    mouseOutLabel() {
      this.selectedKeyList = ["1", "2", "3", "4", "5", "6", "7"];
    }
  },
  props: ["jsonData"]
};
</script>

<style scoped>
.filter-options {
  display: inline-block;
  width: 25%;
  text-align: center;
}
.filter-options-container {
  width: 75%;
  background-color: lightyellow;
}
#app2 {
  background-color: lightgray;
  padding: 2%;
  border-radius: 7%;
}
</style>
