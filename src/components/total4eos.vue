<template>
    <line-chart
        :options="{responsive: false,
         cubicInterpolationMode: 'monotone',
         scales: {
					xAxes: [{
						display: true,
						scaleLabel: {
							display: true,
							labelString: '储备金'
						}
					}],
					yAxes: [{
						display: true,
						scaleLabel: {
							display: true,
							labelString: '发行量'
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
import config from'./config.json'

export default {
  name: 'ChartForToken',
  props: {
    // step: {type: Number, default: 1000},
    // limits: {type: Number, default: 2000},
  //  step: {type: Number, default: config.step},
    // limits: {type: Number, default: config.limits},
   //  k: {type: Number, required: true},
    cw: config.cw,
    supply: config.supply,
    balance: config.balance   
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
  created(){
    this.step = config.step;
    this.limits = config.step;
    this.cw = config.cw;
    this.supply = config.supply;
    this.balance = config.balance;
    this.eop= config.eop;        
  },
  methods: {
    convert_to_exchange(x) {
//        x *= eop;
        let {cw, supply, balance, eop} = this
        x *= eop;        
        let r = -supply * (1.0 - Math.pow(1 + x/(balance + x), cw ));
        return r;
    },

    gen() {
      let {balance, step, supply} = this
      const chartData = {
        labels: []
      }
      let data = []
      const length = 1
      
      for (let x = balance; x <= 1500; x += step) {
        chartData.labels.push(x);
        let hpy = this.convert_to_exchange(x - balance) + supply;      
        data.push(hpy)
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
