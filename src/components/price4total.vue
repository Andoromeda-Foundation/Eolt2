<template>
    <line-chart
        :options="{responsive: false,
         cubicInterpolationMode: 'monotone',
         scales: {
					xAxes: [{
						display: true,
						scaleLabel: {
							display: true,
							labelString: '发行量'
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
    // step: {type: Number, default: 1},
    // limits: {type: Number, default: 50},
    //step: config.step,
    // limits: config.step,
    k: {type: Number, required: true},
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


        let {cw, supply, balance, eop} = this
        x *= eop;
        let r = -supply * (1.0 - Math.pow(1 + x/(balance + x), cw ));
        return r;
    },

/*

    getPrice(x) {
        let {cw, supply, balance} = this

        supply += this.convert_to_exchange(x);
        balance += x;

        x = 0.0001;
        let r = -supply * (1.0 - Math.pow(1 + x/(balance + x), cw));
        return x / r;
    },  */

    getPrice(x) {
        let {cw, supply, balance} = this
        supply += this.convert_to_exchange(x);
        return (balance + x) / (supply * cw ) ;
    },    

    mathFns (x) {
      const { k } = this
      return (0.2 + k * x) * x / 2
    },


    /*gen() {
      let {balance, step, supply} = this
      const chartData = {
        labels: []
      }
      let data = []
      const length = 1
      
      for (let x = balance; x <= supply; x += step) {
//        const eos = this.mathFns(x)
        let eos = this.convert_to_exchange(x);
        let price = this.getPrice(x - 1000);      
        chartData.labels.push(eos);
        data.push(price);
      }
      return {chartData, data};
    }, */

    gen() {
   let {balance, step, supply} = this
      const chartData = {
        labels: []
      }
      let data = []
      
      for (let x = balance; x <= 1500; x += 1) {
        let hpy = this.convert_to_exchange(x - balance) + supply;      
        chartData.labels.push(hpy);
        let price = this.getPrice(x - balance);
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
