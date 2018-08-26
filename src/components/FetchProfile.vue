<template>
    <div class="balance">
      <line-chart
        :options="{responsive: true,
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
				}
         }"
        :chartData="chartData" v-if="chartData"
        >
      </line-chart>
      <button @click="fetchRandomData"> RANDOM DATA </button>
      <h2 class="subtitle">Account: {{ account_name }}, Balance: {{ balance.eos }} </h2>
        <ul>
            <li v-for="info in userinfos" :key="info.account">
                {{ info.account }} Have {{ info }} Chips in the pocket
            </li>
        </ul>
    </div>
</template>

<script>
import Eos from 'eosjs'
import { eosOptions } from '../config'
import LineChart from './LineChart'
const eosOption = eosOptions['kylin']

export default {
  props: ['account_name', 'symbol'],
  components: {
    LineChart
  },
  data: () => ({
    eos: null,
    balance: { eos: null },
    userinfos: [],
    chartData: null
  }),
  created () {
    this.eosClient = Eos(eosOption)
    this.getCurrencyBalance()
  },
  methods: {
    mathFns ({ k, x }) {
      return (0.2 + k * x) * x / 2
    },
    fetchRandomData () {
      const [k, step] = [1.5, 100] // fake K is 1.5
      const chartData = {
        labels: []
      }
      const data = []
      const length = 20
      for (let idx = 1; idx <= 20 * step + 1; idx += step) {
        chartData.labels.push(idx)
        const eos = this.mathFns({ k, x: idx })
        data.push(eos)
      }
      chartData.datasets = [
        {
          label: 'Price',
          // backgroundColor: '#f87979',
          data
        }
      ]
      this.chartData = chartData
    },
    getCurrencyBalance () {
      const { account_name, symbol, balance } = this
      // 获取EOS
      this.eosClient
        .getCurrencyBalance({
          code: 'eosio.token',
          account: account_name
        })
        .then(
          res => {
            const eos = res[0]
            this.balance = Object.assign(balance, { eos })
          },
          res => {
            console.log(res)
          }
        )

      this.eosClient
        .getTableRows({
          json: 'true',
          code: 'happyeosslot',
          // scope: 'happyeosslot',
          scope: account_name,
          table: 'accounts',
          limit: 10,
          lower_bound: 0
        })
        .then(data => {
          this.userinfos = data.rows[0]
        })
        .catch(e => {
          console.error(e)
        })
    }
  }
}
</script>
