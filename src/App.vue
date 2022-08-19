<template>
  <div id="app">
    <!-- in layout was "Copy the best masters" and "Copy best masters"-->
    <h1>Copy the best masters</h1>
    <!-- only when width more 600 -->
    <div class="tradings-block" v-if="window_w > 600">
      <div class="tradings-list">
        <div class="trading-switch" :class="selectedTrading === trading ? 'selected' :
''" @click="tradingSwitch(trading)" v-for="(trading, ind) in fourTradings" v-bind:key="'trading' + ind">
          <div class="flag" v-if="trading"><img :src="trading.flag" alt="flag"></div>
          <div class="info" v-if="trading">
            <div class="name">{{ind + 1}}. {{trading.name}}</div>
            <div class="profit">+ {{trading.monthly_profit}}%</div>
          </div>
        </div>
      </div>
      <trading-component :trading="selectedTrading"/>
    </div>
    <!-- if width less than 600 -->
    <div class="small-screen" v-else>
      <!-- transition-group for animation -->
      <transition-group name="swiper" mode="in-out" class="swiper">
        <trading-mobile ref="trading" :trading="trading" :numb="ind" v-for="(trading, ind) in fourTradings" v-show="!selectedTrading || selectedTrading === trading" v-bind:key="'trading' + ind"/>
      </transition-group>
      <!-- carousel indicator-->
      <div class="switcher">
        <span :class="selectedTrading === trading ? 'selected' : ''" @click="tradingSwitch(trading)" v-for="(trading, ind) in fourTradings" v-bind:key="'switchto' + ind"></span>
      </div>
    </div>
  </div>
</template>

<script>
import TradingComponent from '@/components/TradingComponent';
import TradingMobile from '@/components/TradingMobile';

export default {
  name: 'App',
  components: {
    TradingComponent,
    TradingMobile
  },
  data() {
    return {
      // all tradings in json
      tradings: [],
      selectedTrading: null,
      // for indicator when width less than 600
      window_w: window.innerWidth
    }
  },
  async mounted () {
    // read json
    let list;
    await fetch('input.json').then(response => response.json()).then(json => { list = JSON.parse(JSON.stringify(json))});
    this.tradings = list
    // first in list selected automatically
    this.selectedTrading = this.fourTradings[0]
  },
  computed: {
    fourTradings () {
      let list = [];
      // get 4 random tradings
      while (list.length < 4) {
        let ind = Math.floor(Math.random() * (this.tradings.length + 1));
        // check if this trading added
        let find = list.find(element => { return element ? element.name === this.tradings[ind].name : false});
        if (!find) {
          list.push(this.tradings[ind]);
        }
      }
      return list
    }
  },
  methods: {
    // click on element in list tradings or span in switcher
    tradingSwitch(t_obj) {
      this.selectedTrading = t_obj;
    }
  }
}
</script>

<style>
@font-face {
  font-family: 'Gotham Pro';
  font-style: normal;
  font-weight: 500;
  src: url("./assets/fonts/GothamPro-Medium.woff") format("woff");
}

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  padding: 0;
  overflow: hidden;
}

#app {
  font-family: 'Roboto', sans-serif;
  color: #0A071E;
  margin-top: 60px;
  padding: 20px;
}

h1 {
  font-family: 'Gotham Pro', sans-serif;
  font-weight: 500;
  font-size: 48px;
  max-width: 1180px;
  margin: 0.67em auto;
}

.tradings-block {
  display: flex;
  justify-content: left;
  padding: 13px 0;
  gap: 29px;
  max-width: 1180px;
  margin: auto;
  flex-wrap: wrap;
}

.trading-switch {
  padding: 23px 24px;
  border-radius: 25px;
  background-color: white;
  transition: all 0.2s ease;
  display: flex;
  gap: 16px;
  font-weight: 500;
  margin-bottom: -1px
}

.trading-switch.selected {
  background-color: #F5F7F9;
}

.trading-switch:active {
  background-color: #F5F7F9;
}

.trading-switch .flag {
  border-radius: 50%;
  width: 48px;
  height: 48px;
  overflow: hidden;
  margin-top: 4px;
}

.trading-switch .flag img {
  min-width: 48px;
  min-height: 48px;
  object-fit: cover;
}

.trading-switch .name {
  font-size: 20px;
  font-weight: 500;
}

.trading-switch .profit {
  font-weight: 500;
  color: #34B428;
  font-size: 20px;
  margin-top: 7px;
}

.small-screen {
  padding: 16px;
  position: relative;
}

.switcher {
  display: flex;
  gap: 8px;
  justify-content: center;
  margin-top: 20px
}

.switcher span {
  border-radius: 26px;
  width: 8px;
  height: 8px;
  background: #DDE0E9;
  transition: all 1s ease;
}

.switcher span.selected {
  background: #C8C8D5;
  width: 20px;
}

.swiper {
  min-width: 100%;
  min-height: 365px;
}

.small-screen {
  min-height: 425px;
}

.switcher {
  position: absolute;
  width: 100%;
  bottom: 0;
}

/* it is for animation on mobile */

.swiper-enter-active{
  position: absolute !important;
  transition: all 1.5s ease !important;
  width: calc(100% - 32px) !important;
}
.swiper-leave-active {
  position: absolute !important;
  transition: all 1.5s ease !important;
  box-shadow: -10px 10px 10px rgba(0, 0, 0, 0.3);
}

.swiper-enter-from{
  z-index: -1 !important;
  position: absolute !important;
  transform: translateX(-120%) !important;
}

.swiper-enter-to{
  z-index: -1 !important;
  position: absolute !important;
  transform: translateX(0) !important;
}

.swiper-leave-to {
  position: absolute !important;
  transform: translateX(120%) !important;
}

.swiper-leave-from {
  position: absolute !important;
  transform: translateX(0) !important;
}

/* for tablets */

@media screen and (max-width: 1000px){
  #app{
    margin-top: 0;
  }

  h1 {
    font-size: 38px
  }

  .trading-switch .name {
    font-size: 16px;
  }

  .trading-switch .profit {
    font-weight: 500;
    color: #34B428;
    font-size: 16px;
    margin-top: 9px;
  }
  .trading-switch .flag {
    width: 41px;
    height: 41px;
  }

  .trading-switch .flag img {
    max-width: 41px;
    min-height: 41px;
  }
}

@media screen and (max-width: 800px) {
  .tradings-list {
    display: flex;
    flex-wrap: wrap;
  }

  .trading-switch {
    flex: 1;
    min-width: 300px;
  }
}

@media screen and (max-width: 600px){
  #app {
    margin-top: 0;
    padding: 14px;
  }
  h1 {
    font-size: 28px;
    padding: 0 16px;
    margin-bottom: 8px;
  }
}

</style>
