<template>
  <!-- only when width more 600 -->
  <section class="trading" v-if="trading">
    <!-- Info of trading -->
    <div class="t-info">
      <div class="t-monthly">
        <div class="t-title">Monthly profit</div>
        <div class="t-value">{{trading.monthly_profit}}%</div>
      </div>
      <div class="t-total">
        <div class="t-title">Total profit</div>
        <div class="t-value">{{trading.total_profit}}%</div>
      </div>
      <div class="management">
        <div class="t-title">In management</div>
        <div class="t-value">{{trading.capital}} USD</div>
      </div>
      <button @click="copyNow" class="copy-now">Copy Now</button>
    </div>
    <!-- Chart -->
    <div class="t-chart" ref="mychart" id="mychart"></div>
  </section>
</template>

<script>
import { createChart} from 'lightweight-charts';
export default {
  name: 'TradingComponent',
  props: {
    trading: {
      type: Object,
      // if in import was error
      default: new Object({
        name: "Something Wrong",
        total_profit: 0,
        monthly_profit: 0,
        capital: "0.1",
        flag: "https://flagcdn.com/h40/ru.png",
        chart: [0, 14.11, 28.22, 40.17, 56.44, 70.92, 95.09, 130.28, 137.02, 168.74, 208.88, 198.69, 165.77, 168.44, 111.12, 138.7, 31.74, 42.74, 45.07, 31.55, 20.25, 123.29, 175.2, 175.92, 156, 107.67, 111.95, 141.38, 166.31, 214.81, 224.45, 239.35, 262.89, 305.13, 408.09, 472.12, 338.01, 196.99, 183.58, 71.23]
      })
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
  updated() {this.createGraph()},
  methods: {
    // click on Copy Now
    copyNow() {
      console.log(JSON.stringify(this.trading));
      console.log(this.$refs.mychart);
    },
    // create chart
    createGraph() {
      console.log('created graph')
      if (this.chart && this.lineSeries) {
        this.chart.removeSeries(this.lineSeries);
      } else {
        this.chart = createChart(this.$refs.mychart, {
          layout: {
            textColor: "#81818D",
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
            borderColor: '#DDE0E9'
          },
          timeScale: {
            borderColor: '#DDE0E9'
          },
          crosshair: {
            vertLine: {
              width: 1,
              color: '#DDE0E9',
              style: 3,
            },
            horzLine: {
              width: 1,
              color: '#DDE0E9',
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
        data.push({
          time: date.toISOString().split('T')[0],
          value: this.trading.chart[i]
        });
      }
      return data;
    }
  }
}
</script>

<style scoped>
.trading {
  border: 1px solid #E8EAED;
  border-radius: 20px;
  padding: 29px 23px;
  flex: 1;
}

.trading .t-info {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 15px;
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
  font-size: 28px;
  color: #080816;
  margin-top: 10px;
}

.trading .copy-now {
  padding: 20px 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  min-width: 112px;
  max-width: 225px;
  background: #8A24F3;
  border: none;
  border-radius: 6px;
  font-weight: 700;
  color: white;
  letter-spacing: 0.06em;
  margin-top: 2px;
  transition: background 0.2s ease;
  flex: 1;
}

.trading .copy-now:hover {
  background: #701BC8;
}

.trading .copy-now:active {
  background: #4F118F;
}

.trading .t-chart {
  margin-top: 15px;
  width: 100%;
  max-width: 789px;
  min-height: 272px;
}

/* Tablets */
@media screen and (max-width: 1000px){
  .trading {
    padding: 19px 23px 24px 23px;
  }

  .trading .t-value {
    font-size: 20px;
  }
}
</style>