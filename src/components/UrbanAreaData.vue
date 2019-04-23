<template lang="html">
  <div id="flex-container" v-if="urbanArea">
    <div class="title">
      <h2>{{ urbanArea.name }}</h2>
      <p>{{ country }}, {{ continent }}</p>
    </div>
    {{ generateChartData }}
    <div class="gchart">
      <GChart
      type="BarChart"
      :data="chartData"
      :options="chartOptions"
      />
    </div>
    <img :src="image">
  </div>

</template>

<script>
import { GChart } from 'vue-google-charts';

export default {
  name: 'urban-area-data',
  props: ['urbanArea', 'continent', 'country', 'image', 'scores'],
  components: {
    "gchart": GChart
  },
  data(){
    return {
      chartData: [],
      chartOptions: {
        width: 1000,
        height: 450,
        colors: ["#f3c32c", "#f3d630", "#f4eb33", "#d2ed31", "#7adc29", "#36cc24", "#19ad51", "#0d6999", "#051fa5", "#150e78", "#3d14a4", "#5c14a1", "#88149f", "#b9117d", "#d10d54", "#e70c26", "#f1351b"],
        chartArea: {
          height: 400
        }
      }
    }
  },
  computed: {
    generateChartData(){
      const data = [];
      const colors = [];
      data.push(this.scores.map(heading => heading["name"]));
      data.push(this.scores.map(score => score["score_out_of_10"]))
      this.chartData = data;
    },
  },
  watch: {
  },
  methods: {

  }
}
</script>

<style lang="css" scoped>

#flex-container {
  display: flex;
  align-items: center;
  flex-direction: column;
}

.gchart {
  padding: 0;
  margin: 0;
  width: 100%;
}

img {
  width: 70%;
  height: auto;
  margin-top: 5rem;
  margin-bottom: 2rem;
}

h2 {
  font-family: 'Nova Round', cursive;
  padding: 5px;
  margin-top: 70px;
  margin-bottom: 0px;
  font-size: 2em;
}

p {
  font-family: 'Nanum Gothic', sans-serif;
  padding: 5px;
  margin-bottom: 15px;
}

.title {
  display: flex;
  flex-direction: column;
  align-items: center;
}

</style>
