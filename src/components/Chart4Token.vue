<template>
    <line-chart
        :options="{responsive: false,
         cubicInterpolationMode: 'monotone',
         scales: {
					xAxes: [{
						display: true,
						scaleLabel: {
							display: true,
							labelString: 'The X'
						}
					}],
					yAxes: [{
						display: true,
						scaleLabel: {
							display: true,
							labelString: 'The EOS'
						}
					}]
				}}"
        :chartData="chartData" v-if="chartData"
          :width="720"
          :height="480"
        >
      </line-chart>
</template>

<script>
import LineChart from './LineChart'

export default {
  name: 'ChartForToken',
  props: {
    step: {type: Number, default: 100},
    k: {type: Number, required: true}
  },
  components: {
    LineChart
  },
  data: () => ({
    chartData: null
  }),
  mounted () {
    this.getSomeData()
  },
  methods: {
    mathFns (x) {
      const { k } = this
      return (0.2 + k * x) * x / 2
    },
    getSomeData () {
      const {k, step} = this
      const chartData = {
        labels: []
      }
      const data = []
      const length = 20
      for (let x = 0; x <= 20 * step; x += step) {
        chartData.labels.push(x)
        const eos = this.mathFns(x)
        data.push(eos)
      }
      chartData.datasets = [
        {
          label: 'Price',
          borderColor: '#1890ff',
          backgroundColor: 'rgba(0, 0, 0, 0)',
          data
        },
        {
          label: 'Price 1',
          borderColor: 'red',
          backgroundColor: 'rgba(0, 0, 0, 0)',
          data: data.map(p => p - 500000)
        }
      ]
      this.chartData = chartData
    }
  }
}
</script>
