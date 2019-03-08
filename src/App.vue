<template>
  <div>
    <div id="app">
      <Chart :jsonData="jsonData"></Chart>
    </div>
  </div>
</template>

<script>
import JSONData from "./assets/result_action_plans.json";
import Chart from "./components/Chart";
export default {
  name: "app",
  components: {
    Chart
  },
  data() {
    return {};
  },
  computed: {
    jsonData() {
      return JSONData;
    }
  },
  methods: {}
};
</script>

<style>
</style>
