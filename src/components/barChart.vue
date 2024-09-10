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
  },
  gradiationValues: {
  }
},
watch: { 
  gradiationValues:{ 
    handler: function(val, oldVal){
      const chartInstance = this.$refs.bar.chart
      chartInstance.update()
    },
    deep: true
  }
},
data(){
  return{
    horizontalLinePlugin: {
            id: 'horizontalLine',
            afterDraw: (chart) => {
              console.log(chart)
              const yValueMax = chart.scales.y.max
              const yValueMin = chart.scales.y.min
              const xValueMax = chart.scales.x.max
              const xValueMin = chart.scales.x.min
              const { ctx } = chart
              ctx.lineWidth = 2;
              ctx.fillStyle = "red"

              ctx.beginPath();

              const yTemp1 = chart.scales.y.getPixelForValue(Math.floor((yValueMax - yValueMin) * (this.gradiationValues[1] / 255)))
              const xTemp1 = chart.scales.x.getPixelForValue(Math.floor((xValueMax - xValueMin) * (this.gradiationValues[0] / 255)))
              ctx.moveTo(chart.chartArea.left, yTemp1);
              ctx.lineTo(xTemp1, yTemp1);

              const yTemp2 = chart.scales.y.getPixelForValue(Math.floor((yValueMax - yValueMin) * (this.gradiationValues[3] / 255)))
              const xTemp2 = chart.scales.x.getPixelForValue(Math.floor((xValueMax - xValueMin) * (this.gradiationValues[2] / 255)))
              ctx.lineTo(xTemp2, yTemp2);

              ctx.lineTo(chart.chartArea.right, yTemp2)

              ctx.strokeStyle = 'red';
              ctx.lineWidth = 2;
              ctx.stroke();

              ctx.beginPath()
              ctx.arc(xTemp1, yTemp1, 4, 0, 2 * Math.PI);
              ctx.fill();
              ctx.stroke();

              ctx.beginPath()
              ctx.arc(xTemp2, yTemp2, 4, 0, 2 * Math.PI);
              ctx.fill();
              ctx.stroke();
            }
        }
  }
},
mounted(){
  ChartJS.register(this.horizontalLinePlugin)
}
}
</script>