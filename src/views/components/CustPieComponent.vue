// PieChart.vue
<template>
  <div class="card z-index-2">
    <canvas ref="chart" width="400" height="400"></canvas>
  </div>
</template>

<script>
import { Pie } from 'vue-chartjs';

export default {
  extends: Pie,
  props: ['chartData', 'options'],
  mounted() {
    this.renderChart(this.chartData, this.options);
  },
  watch: {
    chartData: function(newData) {
      this.renderChart(newData, this.options);
    },
  },
};
</script>