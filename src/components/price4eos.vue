<template>
    <line-chart
        :options="{responsive: false,
         cubicInterpolationMode: 'monotone',
         scales: {
					xAxes: [{
						display: true,
						scaleLabel: {
							display: true,
							labelString: '价格'
						}
					}],
					yAxes: [{
						display: true,
						scaleLabel: {
							display: true,
							labelString: 'EOS'
						}
					}]
				}}"
        :chartData="chartData" v-if="chartData"
          :width="960"
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
    limits: {type: Number, default: 2000},
    k: {type: Number, required: true},
    cw: 0.5,
    supply: 1000,
    balance: 1000    
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
    convert_to_exchange(x) {
        let cw = 0.5;
        let supply = 1000;
        let balance = 1000;
        let r = -supply * (1.0 - Math.pow(1 + x/(balance + x), cw ) );
        return r;
    },

    getPrice(x) {
        let cw = 0.5;
        let supply = 1000;
        let balance = 1000;

        supply += this.convert_to_exchange(x);
        balance += x;

        x = 0.0001;
        let r = -supply * (1.0 - Math.pow(1 + x/(balance + x), cw ) );
        return x / r;
    },    

    gen() {
      const {k, step, limits} = this
      const chartData = {
        labels: []
      }
      let data = []
      const length = 1
      
      for (let x = 1000; x <= 2000; x += 10) {
        chartData.labels.push(x);
        let price = this.getPrice(x - 1000);
        data.push(price);
      }
      return {chartData, data};
    },

    
    getSomeData () {
      let {chartData, data} = this.gen();

      chartData.datasets = [
        {
          label: 'Price',
          borderColor: '#1890ff',
          backgroundColor: 'rgba(0, 0, 0, 0)',
          pointRadius: 0,
          data
        }/*,

        {
          label: 'Price 1',
          borderColor: 'red',
          backgroundColor: 'rgba(0, 0, 0, 0)',
          pointRadius: 0,
          data: data.map(p => p - 500000)
        }*/
      ]
      this.chartData = chartData
    }
  }
}
</script>
