<template>
  <div id="app">
    <p style="font-size: 40px; color: #006464">
      <em> <strong>Sankey Chart by Black Panther </strong> </em>
    </p>
    <SankeyChart />
  </div>
</template>

<script>
import SankeyChart from "./components/SankeyChart.vue";

export default {
  name: "App",
  components: {
    SankeyChart,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
