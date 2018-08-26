<template>
    <line-chart
        :options="{responsive: false,
         cubicInterpolationMode: 'monotone',
         scales: {
					xAxes: [{
						display: true,
						scaleLabel: {
							display: true,
							labelString: 'EOS'
						}
					}],
					yAxes: [{
						display: true,
						scaleLabel: {
							display: true,
							labelString: '价格'
						}
					}]
				}}"
        :chartData="chartData" v-if="chartData"
          :width="1280"
          :height="480"
        >
      </line-chart>
</template>

<script>
import LineChart from './LineChart'

export default {
  name: 'ChartForToken',
  props: {
    step: {type: Number, default: 1},
    limits: {type: Number, default: 50},
    k: {type: Number, required: true},
    cw: 0.5,
    supply: 1000,
    balance: 1000    
  },

  paras: {

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
        const {cw, supply, balance} = this
        let r = -supply * (1.0 - Math.pow(1 + x/(balance + x), cw ) );
        return r;
    },

    getPrice(x) {
        const {cw, supply, balance} = this
        supply += this.convert_to_exchange(x);
        balance += x;
        x = 0.0001;
        let r = -supply * (1.0 - Math.pow(1 + x/(balance + x), cw ) );
        return x / r;
    },    

    mathFns (x) {
      const { k } = this
      return (0.2 + k * x) * x / 2
    },

    gen() {
      const {k, step, limits} = this
      const chartData = {
        labels: []
      }
      let data = []
      const length = 1
      
      for (let x = 0; x <= 1000000; x += 10000) {
        chartData.labels.push(x);
        let eos = this.convert_to_exchange(x);        
        data.push(eos)
      }
      return {chartData, data};
    },

    gen2() {
      const {k, step, limits} = this
      const chartData = {
        labels: []
      }
      let data = []
      const length = 1
      
      for (let x = 0; x <= 10000000; x += 100000) {
        chartData.labels.push(x);
//        const eos = this.mathFns(x)
        let price = this.getPrice(x);
        data.push(price);
      }
      return {chartData, data};
    },
    
    getSomeData () {
      let {chartData, data} = this.gen ();

      chartData.datasets = [
        {
          label: 'Price',
          borderColor: '#1890ff',
          backgroundColor: 'rgba(0, 0, 0, 0)',
          pointRadius: 0,
          data
        }
      ]
      this.chartData = chartData
    }
  }
}
</script>
