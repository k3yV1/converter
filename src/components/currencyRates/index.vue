<template>
  <div class="currency-rates-component">
    <div class="currency-rates-component__box-wrapper">
      <div class="box-wrapper__title">
        <span>Currency Rates</span>
      </div>
      <div class="box-wrapper__rates">
        <div class="rates__first-part">
          <select v-model="defaultInterestingCurrency" @change="loadCurrency">
            <option
              v-for="(currency, index) in interestingCurrency"
              :value="currency"
              :key="index"
            >
              {{ currency }}
            </option>
          </select>
        </div>
        <div class="rates__second-part">
          <table>
            <thead>
              <tr>
                <th>#</th>
                <th>Currency Code</th>
                <th>Currency Value</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(value, key, index) in rates" :key="key">
                <td>{{ index + 1 }}</td>
                <td>{{ key }}</td>
                <td>{{ value }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <div class="btn__open-popup">
      <button @click="showModal">Add Currency</button>
      <modal
        v-if="isModalVisible"
        :title="popupTitle"
        :currencies="currencies"
        @addCurrency="addCurrency"
        @close="hideModal"
      >
        <template v-slot:body></template>
      </modal>
      <button :class="{'disabled': isDisabled}" :disabled="isDisabled" @click="disableButton">Refresh</button>
    </div>
    <div class="disable-text">
      <p v-if="isDisabled === true">Идет обновление курсов валют...</p>
    </div>
  </div>
</template>

<script>
import Modal from "@/components/modal/Modal.vue";
export default {
  components: {
    Modal,
  },

  data: () => ({
    popupTitle: "Search Currency",
    amount: "",
    defaultInterestingCurrency: "USD",
    interestingCurrency: ["USD", "EUR", "UAH"],
    currencies: ["USD", "EUR", "UAH", "BTC"],
    rates: {},
    isModalVisible: false,
    updating: false,
    updateInterval: null,
    updateIntervalTime: 5000,
    lastUpdate: null,
    isDisabled: false,
    newCurrency: "",
  }),

  computed: {
    localStorageCurrencies() {
      const currencies = JSON.parse(localStorage.getItem("currencies")) || [];
      return currencies.join(", ");
    },
  },

  methods: {
    loadCurrency() {
      const url = `https://api.apilayer.com/exchangerates_data/latest?&symbols=${this.currencies.join()}&base=${
        this.defaultInterestingCurrency
      }`;
      fetch(url, {
        headers: {
          "Content-Type": "application/json",
          apikey: "TD4g0jDDYziBqjghae347S98c6S1VaCu",
        },
      })
        .then((response) => response.json())
        .then((data) => {
          console.log('data: ', data);
          this.rates = data.rates;
          localStorage.setItem("rates", JSON.stringify(this.rates));
        })
        .catch((error) => {
          console.log(error);
        })
    },

    addCurrency(currency) {
      if (!this.currencies.includes(currency)) {
        this.currencies.push(currency);
        localStorage.setItem("currencies", JSON.stringify(this.currencies));
        this.loadCurrency();
        this.hideModal();
      }
    },

    updateRates() {
      if (!this.isUpdating) {
        this.isUpdating = true;
        this.loadCurrency();
      }
    },

    showModal() {
      this.isModalVisible = true;
    },

    hideModal() {
      this.isModalVisible = false;
    },

    disableButton() {
      this.isDisabled = true;
      setTimeout(() => {
        this.enableButton();
        this.loadCurrency();
      }, 5000)
    },

    enableButton() {
      this.isDisabled = false;
    }
  },

  mounted() {
    this.loadCurrency();
  },

  created() {
    const savedCurrencies = JSON.parse(localStorage.getItem("currencies"));
    if (savedCurrencies && Array.isArray(savedCurrencies)) {
      this.currencies = this.currencies.concat(savedCurrencies);
    }

    this.loadCurrency();
  },
};
</script>

<style lang="scss" src="./styles/style.scss">
</style>