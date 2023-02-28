<template>
  <div class="converter-component">
    <div class="container">
      <div class="converter-component__box">
        <div class="box__title">
          <span>Currency Converter</span>
        </div>
        <div class="box__converter">
          <div class="converter__select-initial-currency item">
            <select v-model="fromCurrency" @change="updateConversion">
              <option
                v-for="(currency, index) in currencies"
                :value="currency"
                :key="index"
              >
                {{ currency }}
              </option>
            </select>
            <input
              type="number"
              v-model="fromAmount"
              @keyup="updateConversion"
              placeholder="amountFrom"
              min="0"
              max="maxValue"
            />
          </div>
          <div class="converter__select-desired-currency item">
            <input
              type="number"
              v-model="toAmount"
              @keyup="updateConversion"
              placeholder="amountTo"
            />
            <select v-model="toCurrency" @change="updateConversion">
              <option
                v-for="(currency, index) in currencies"
                :value="currency"
                :key="index"
              >
                {{ currency }}
              </option>
            </select>
          </div>
        </div>
        <div class="box__rates">
          <CurrencyRates />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import CurrencyRates from "./currencyRates"
export default {
  components: {
    CurrencyRates
  },

  data() {
    return {
      currencies: ["USD", "EUR", "UAH", "GBP", "BTC", "ETH", "BNB", "XRP"],
      fromCurrency: "USD",
      toCurrency: "BTC",
      fromAmount: 1,
      toAmount: "",
      exchangeRate: 0,
    };
  },

  computed: {
    maxValue() {
      return 10000 - this.fromAmount;
    }
  },

  methods: {
    updateConversion() {
      const url = `https://api.apilayer.com/fixer/latest?&base=${this.fromCurrency}&symbols=${this.toCurrency}`;
      fetch(url, {
        headers: {
          "Content-Type": "application/json",
          apikey: "vxd7LY4mnAh2T08RuDLG2GeEGUjDbsix",
        },
      })
        .then((response) => response.json())
        .then((data) => {
          console.log("data: ", data);
          this.exchangeRate = data.rates[this.toCurrency];
          this.toAmount = (this.fromAmount * this.exchangeRate).toFixed(2);
        })
        .catch((error) => {
          console.log(error);
        });
    }
  },
  mounted() {
    this.updateConversion();
  },
};
</script>

<style lang="scss">
@import '@/assets/global.scss';
</style>