<template>
  <div class="">
    <table class="mdl-data-table mdl-js-data-table mdl-shadow--2dp">
      <thead>
      <tr>
        <th>#</th>
        <th class="mdl-data-table__cell--non-numeric">Name</th>
        <th>Price(EUR)</th>
        <th>Today</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="coin in coins">
        <td>{{ coin.rank }}</td>
        <td class="mdl-data-table__cell--non-numeric image-card__picture">
          <img v-bind:src="getCoinImage(coin.symbol)">
          <span class="mdl-cell--hide-phone">{{ coin.name }}</span>
          <span class="mdl-cell--hide-desktop mdl-cell--hide-tablet">{{ coin.symbol }}</span>
        </td>
        <td>{{ Math.round(coin.price_eur*100)/100 }}</td>
        <td v-bind:style="getColor(coin.percent_change_24h)">
          <span v-if="coin.percent_change_24h > 0">+</span>{{ coin.percent_change_24h }}%
        </td>
      </tr>
      </tbody>
    </table>
    <button class="add-picture-button mdl-button mdl-js-button mdl-button--fab mdl-js-ripple-effect mdl-button--colored" v-on:click="addToMyCoins(coin)">
      <i class="material-icons">add</i>
    </button>

  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    name: 'home',
    data() {
      return {
        coins: [],
        coinData: {},
        myCoins: [],
      };
    },
    created() {
      this.getCoinData();
      /**
       * Start the polling every minute
       */
      setInterval(() => {
        this.getCoins();
      }, 60000);
    },
    methods: {
      /**
       * Load up all cryptocurrency data.  This data is used to find what logos
       * each currency has, so we can display things in a friendly way.
       */
      getCoinData() {
        axios.get('https://www.cryptocompare.com/api/data/coinlist')
          .then((response) => {
            // JSON responses are automatically parsed.
            this.coinData = response.data.Data;
            this.getCoins();
          })
          .catch((e) => {
            this.getCoins();
            this.errors.push(e);
          });
      },

      /**
       * Get the top 10 cryptocurrencies by value.
       */
      getCoins() {
        axios.get('https://api.coinmarketcap.com/v1/ticker/?convert=EUR&limit=10')
          .then((resp) => {
            this.coins = resp.data;
          })
          .catch((err) => {
            console.error(err);
          });
      },

      /**
       * Given a cryptocurrency ticket symbol, return the currency's logo
       * image.
       */
      getCoinImage(symbol) {
        if (this.coinData[symbol]) {
          return `https://www.cryptocompare.com${this.coinData[symbol].ImageUrl}`;
        }
        return '';
      },
      getCoinURL(symbol) {
        if (this.coinData[symbol]) {
          document.href = `https://www.cryptocompare.com${this.coinData[symbol].Url}`;
        }
        return '';
      },
      /**
       * Return a CSS color (either red or green) depending on whether or
       * not the value passed in is negative or positive.
       */
      getColor(num) {
        return num > 0 ? 'color:green;' : 'color:red;';
      },
      addToMyCoins(coin) {
        this.myCoins.push(coin);
      },
    },
    components: {
    },
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #35495E;
}

table {
  margin: 0 auto;
}

.add-picture-button {
  position: fixed;
  right: 24px;
  bottom: 24px;
  z-index: 998;
}

.image-card__picture > img {
  width:18px;
  margin-right:10px;
}
</style>
