<template>
    <div class="balance">
      <price4eos :k="10" :step="10" :limits="100" />
      <h2 class="subtitle">Account: {{ account_name }}, Balance: {{ balance.eos }} </h2>
      <ul>
          <li v-for="info in userinfos" :key="info.account">
              {{ info.account }} Have {{ info }} Chips in the pocket
          </li>
      </ul>
      <price4total :k="10" :step="10" :limits="100" />
      <h2 class="subtitle">Account: {{ account_name }}, Balance: {{ balance.eos }} </h2>
      <ul>
          <li v-for="info in userinfos" :key="info.account">
              {{ info.account }} Have {{ info }} Chips in the pocket
          </li>
      </ul>
      <total4eos :k="10" :step="10" :limits="100" />
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
import price4eos from './price4eos'
import price4total from './price4total'
import total4eos from './total4eos'

const eosOption = eosOptions['kylin']

export default {
  props: ['account_name', 'symbol'],
  components: {
    price4eos,
    price4total,
    total4eos
  },
  data: () => ({
    eos: null,
    balance: { eos: null },
    userinfos: []
  }),
  created () {
    this.eosClient = Eos(eosOption)
    this.getCurrencyBalance()
  },

  methods: {
    async getCurrencyBalance () {
      const { account_name, symbol, balance } = this
      try {
        const curBal = await this.eosClient.getCurrencyBalance({
          code: 'eosio.token',
          account: account_name
        })
        const eos = curBal[0]
        this.balance = Object.assign(balance, { eos })
        const {rows} = await this.eosClient.getTableRows({
          json: 'true',
          code: 'happyeosslot',
          scope: account_name,
          table: 'accounts',
          limit: 10,
          lower_bound: 0
        })
        this.userinfos = rows[0]
      } catch (error) {
        console.error(error)
      }
    }
  }
}
</script>
