<template>
  <div class="balance">
    <div class="tabs">
      <ul>
    			<li 
        			v-for="(item,index) in tabsParam" 
        			:class="{active:item == nowIndex}"
        			@click="toggleTabs">
					<a data-toggle="tab">
            			{{item}}
        			</a>
				</li>
			</ul>
    </div>
		<div class="room-list" v-if="nowIndex==tabsParam[0]">
			<div class="container">
				<total4eos :k="10" :step="10" :limits="100" />
        <h2 class="subtitle">Account: {{ account_name }}, Balance: {{ balance.eos }} </h2>
        <ul>
            <li v-for="info in userinfos" :key="info.account">
                {{ info.account }} Have {{ info }} Chips in the pocket
            </li>
        </ul>
			</div>
		</div>
    <div class="room-list" v-if="nowIndex==tabsParam[1]">
      <price4eos :k="10" :step="10" :limits="100" />
      <h2 class="subtitle">Account: {{ account_name }}, Balance: {{ balance.eos }} </h2>
      <ul>
          <li v-for="info in userinfos" :key="info.account">
              {{ info.account }} Have {{ info }} Chips in the pocket
          </li>
      </ul>
    </div>
    <div class="room-list" v-if="nowIndex==tabsParam[2]">
      <price4total :k="10" :step="10" :limits="100" />
      <h2 class="subtitle">Account: {{ account_name }}, Balance: {{ balance.eos }} </h2>
      <ul>
          <li v-for="info in userinfos" :key="info.account">
              {{ info.account }} Have {{ info }} Chips in the pocket
          </li>
      </ul>
    </div>
    <slider :min=0 :max=100 v-model = "per"></slider>
  </div>
</template>

<script>
import Eos from 'eosjs'
import { eosOptions } from '../config'
import price4eos from './price4eos'
import price4total from './price4total'
import total4eos from './total4eos'
import slider from './Slider'
import config from './config.json'

const eosOption = eosOptions['kylin']

export default {
  props: ['account_name', 'symbol'],
  components: {
    price4eos,
    price4total,
    total4eos,
    slider
  },
  data: () => ({
    eos: null,
    balance: { eos: null },
    userinfos: [],
    per:0,
    config:config
  }),
  created () {
    this.eosClient = Eos(eosOption)
    this.nowIndex = '发行量/储备金';
    this.tabsParam = ['发行量/储备金','价格/储备金','价格/发行量'];
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
    },
  toggleTabs: function(e){
		this.nowIndex = e.target.innerText;
		if (!this.tabsParam.includes(this.nowIndex)) this.nowIndex = '发行量/储备金';
		this.$forceUpdate();
	},
  }
}
</script>
