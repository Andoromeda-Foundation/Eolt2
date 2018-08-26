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
    step: {type: Number, default: 100},
    limits: {type: Number, default: 50},
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

            convert_to_exchange(x) {
                let cw = 0.0005;
                let supply = 1000000000;
                let balance = 500000000;
                let r = -supply * (1.0 - Math.pow(1 + x/(balance + x), cw ) );

                //let r = Math.pow(2.0, x);
                return r;
                // return 2*x;

                /*
                real_type R(supply.amount);
                real_type C(c.balance.amount + in.amount);
                real_type F(c.weight / 1000.0);
                real_type T(in.amount);
                real_type ONE(1.0);

                real_type E = -R * (ONE - pow(ONE + T / C, F));
                int64_t issued = int64_t(E);

                supply.amount += issued;
                c.balance.amount += in.amount;

                return asset(issued, supply.symbol);*/
            },


    mathFns (x) {
      const { k } = this
      return (0.2 + k * x) * x / 2
    },
    getSomeData () {
      const {k, step, limits} = this
      const chartData = {
        labels: []
      }
      const data = []
      const length = 20
      for (let x = 0; x <= 1000; x += step) {
        chartData.labels.push(x);
//        const eos = this.mathFns(x)
        let eos = this.convert_to_exchange(x);
        data.push(eos)
      }
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
