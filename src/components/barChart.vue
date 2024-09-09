<script setup>
import Chart from 'chart.js/auto'
</script>

<template>
  <canvas id="myChart"></canvas>
</template>   

<script>
export default {
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
    const canvas = document.getElementById('myChart')
    const myCh = new Chart(canvas, {
    type: 'bar',
    data: {
      labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
      datasets: [{
        label: '# of Votes',
        data: [12, 19, 3, 5, 2, 3],
        borderWidth: 1
      }]
    },
    options: {}
  });
  const ctx = canvas.getContext("2d")
  ctx.fillStyle = "red";
  ctx.fillRect(0, 0, 1500, 1500);
}
}
</script>