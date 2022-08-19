<template>
  <!-- only when width less than 600
  (another style, chart and order of elements) -->
  <section class="trading" v-if="trading">
    <!-- instead list-->
    <div class="trading-name">
      <div class="flag"><img :src="trading.flag" alt="flag"></div>
      <div class="info">
        <div class="name">{{numb + 1}}. {{trading.name}}</div>
        <div class="profit">+ {{trading.monthly_profit}}%</div>
      </div>
    </div>
    <!-- info of trading -->
    <div class="t-info">
      <div class="t-monthly">
        <div class="t-title">Monthly profit</div>
        <div class="t-value">{{trading.monthly_profit}}%</div>
      </div>
      <div class="t-total">
        <div class="t-title">Total profit</div>
        <div class="t-value">{{trading.total_profit}}%</div>
      </div>
    </div>
    <!-- Chart -->
    <div class="t-chart" ref="mychart" id="mychart"></div>
    <!-- button "Copy Now"-->
    <button @click="copyNow" class="copy-now">Copy Now</button>
  </section>
</template>

<script>
import {createChart} from 'lightweight-charts';
export default {
  name: 'TradingMobile',
  props: {
    trading: {
      type: Object,
      // if in import was error
      default() { return {
        name: "Something Wrong",
        total_profit: 0,
        monthly_profit: 0,
        capital: "0.1",
        flag: "https://flagcdn.com/h40/ru.png",
        chart: [0, 20, 40, 60, 80, 100, 120, 140, 160, 180, 200]
      }}
    },
    // numb in list (for this -> 3. John Doe)
    numb: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      // save to delete chart when redrawing
      chart: null, // save here results of createChart
      lineSeries: null // save here results of addLineSeries
    }
  },
  // create chart when layout is ready
  updated() {console.log('update!'); this.createGraph()},
  methods: {
    // click on Copy Now
    copyNow() {
      console.log(JSON.stringify(this.trading));
      console.log(this.$refs.mychart);
    },
    // create chart
    createGraph() {
      if (this.chart && this.lineSeries) {
        this.chart.removeSeries(this.lineSeries);
      } else {
        this.chart = createChart(this.$refs.mychart, {
          layout: {
            textColor: "white",
            fontSize: 16,
            fontFamily: 'Roboto, sans-serif'
          },
          grid: {
            vertLines: {
              visible: false
            },
            horzLines: {
              visible: false
            }
          },
          rightPriceScale: {
            borderColor: 'white'
          },
          timeScale: {
            borderColor: 'white'
          },
          crosshair: {
            vertLine: {
              width: 1,
              color: 'green',
              style: 3,
            },
            horzLine: {
              width: 1,
              color: 'green',
              style: 3,
            },
          },
          localization: {
            locale: 'en',
            dateFormat: 'dd MMMM'
          }
        });

      }
      this.lineSeries = this.chart.addLineSeries({
        color: '#8A24F3',
        priceFormat: {type: 'percent'},
        crosshairMarkerBackgroundColor: 'white',
        crosshairMarkerBorderColor: '#8A24F3',
        crosshairMarkerRadius: 5,
        lastValueVisible: false,
        priceLineVisible: false,
      });
      this.lineSeries.setData(this.getChartData);
      this.chart.timeScale().fitContent();
    }
  },
  computed: {
    // get data for chart
    getChartData() {
      let data = [];
      for (let i = 0; i < this.trading.chart.length; i++) {
        // because in data didn't have dates
        let date = new Date;
        date.setDate(date.getDate() + i);
        data.push({time: date.toISOString().split('T')[0], value: this.trading.chart[i]});
      }
      return data;
    }
  }
}
</script>

<style scoped>
.trading {
  border: 1px solid #E8EAED;
  border-radius: 11px;
  padding: 19px 23px 24px 23px;
  overflow: hidden;
  background: white;
  width: 100%;
}

.trading .t-info {
  display: flex;
  align-items: flex-start;
  gap: 37px;
  margin-top: 24px;
}

.trading .t-title {
  color: #81818D;
  font-weight: 400;
  font-size: 14px;

}

.trading .t-value {
  font-family: 'Gotham Pro', sans-serif;
  font-style: normal;
  font-weight: 500;
  font-size: 20px;
  color: #080816;
  margin-top: 10px;
}

.trading .copy-now {
  padding: 20px 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  min-width: 112px;
  width: 100%;
  background: #8A24F3;
  border: none;
  border-radius: 6px;
  font-weight: 700;
  color: white;
  letter-spacing: 0.06em;
  margin-top: 2px;
  transition: background 0.2s ease;
}

.trading .copy-now:hover {
  background: #701BC8;
}

.trading .copy-now:active {
  background: #4F118F;
}

.trading .t-chart {
  margin-top: 15px;
  width: calc(100% + 70px);
  max-width: 789px;
  min-height: 129px;
}

.trading-name {
  border-radius: 25px;
  background-color: white;
  transition: all 0.2s ease;
  display: flex;
  gap: 15px;
  font-weight: 500;
  margin-bottom: -1px
}

.trading-name .flag {
  border-radius: 50%;
  width: 41px;
  min-width: 24px;
  height: 41px;
  overflow: hidden;
  margin-top: 4px;
}

.trading-name .flag img {
  max-width: 41px;
  min-height: 41px;
  object-fit: cover;
}

.trading-name .name {
  font-size: 16px;
  font-weight: 500;
}

.trading-name .profit {
  font-weight: 500;
  color: #34B428;
  font-size: 16px;
  margin-top: 9px;
}
</style>