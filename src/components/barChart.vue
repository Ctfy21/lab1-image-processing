<template>
  <Bar
    ref="bar"
    :options="chartOptions"
    :data="chartData"
  >
</Bar>
</template>   

<script>
import { Bar } from 'vue-chartjs'

import { Chart as ChartJS, Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale } from 'chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

export default {
components: { Bar },
props: {
  chartData: {
      type: Object,
      required: true
    },
  chartOptions: {
    type: Object,
    default: () => {}
  }
},
mounted(){
  ChartJS.unregister(horizontalLinePlugin)
  const horizontalLinePlugin = {
            id: 'horizontalLine',
            afterDraw: (chart) => {
              console.log(chart)
              const yValue = chart.scales.y.end
              const { ctx } = chart
              ctx.fillStyle = "red"
              ctx.beginPath();
              ctx.moveTo(chart.chartArea.left, yValue);
              ctx.lineTo(chart.chartArea.right, yValue);
              ctx.strokeStyle = 'red';
              ctx.lineWidth = 2;
              ctx.stroke();
            }
        };
  ChartJS.register(horizontalLinePlugin)
}
}
</script>