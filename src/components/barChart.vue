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
import { nextTick } from 'vue';

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
  mounted(e) {
    nextTick()
    console.log(this.$refs.bar)
  }
}
</script>