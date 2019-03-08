<template>
  <div>
    <div id="app2">
      <MyChart :actions="actions" :data="data"></MyChart>
    </div>

    <div class="filter-options-container">
      <div v-for="actor in actors" v-bind:key="actor.key" class="filter-options">
        <label :for="actor.key">{{actor.value}}</label>
        <input
          type="checkbox"
          :name="actor.value"
          :value="actor.key"
          :id="actor.key"
          @change="inputChanged($event)"
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
      selectedKeyList: []
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
            console.log(this.selectedKeyList.findIndex(k => k === d.key) > -1);
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
