<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
import { mapActions, mapMutations } from 'vuex'
import { networks } from './config'
const network = networks['kylin']
const requiredFields = { accounts: [network] }
import "mint-ui/lib/style.css"
export default {
  data: () => ({

  }),
  methods: {
    ...mapActions(['initScatter']),
    ...mapMutations(['setIdentity']),
    handleScatterLoaded () {
      const scatter = window.scatter
      this.initScatter(scatter)
      this.requestId()
    },
    async suggestNetworkSetting () {
      try {
        await this.scatter.suggestNetwork(network)
      } catch (error) {
        console.info('User canceled to suggestNetwork')
      }
    },
    async requestId () {
      await this.suggestNetworkSetting()
      const identity = await scatter.getIdentity(requiredFields)
      this.setIdentity(identity)
    }
  },
  created () {
    document.addEventListener('scatterLoaded', scatterExtension => {
      console.log('scatterLoaded')
      this.handleScatterLoaded()
    })
  }
}
</script>

<style scoped>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  min-height: 60px;
  padding: 24px 0;
  border: none;
  margin-bottom: 0;
}
.navbar {
  background: none;
}
.navbar-default {
  background-color: #f8f8f8;
  border-color: #e7e7e7;
}
.navbar {
  position: relative;
  min-height: 50px;
  margin-bottom: 20px;
  border: 1px solid transparent;
}
.container {
  width: 100%;
  padding-right: 15px;
  padding-left: 15px;
  margin-right: auto;
  margin-left: auto;
}
#nav .navbar-header {
  text-align: left;
  width: 100%;
  margin: 0;
  position: relative;
}
#nav .navbar-brand {
  float: left;
  color: inherit;
  height: auto;
  padding: 0;
  margin: 0;
  white-space: nowrap;
  min-width: 160px;
  zoom: 1;
}
#nav .navbar-nav > li {
  background: #DCE1ED;
  margin-left: 0;
  margin-right: 2px;
  margin-bottom: 2px;
}
.nav>li {
  position: relative;
  display: block;
}
ul, menu, dir {
  display: block;
  list-style-type: disc;
  -webkit-margin-before: 1em;
  -webkit-margin-after: 1em;
  -webkit-margin-start: 0px;
  -webkit-margin-end: 0px;
  -webkit-padding-start: 40px;
}
</style>
