<template>
  <div class="popup">
    <div class="popup-inner">
      <div class="popup-header">
        <slot name="header">
          <div class="header__title">
            <h3>{{ title }}</h3>
          </div>
          <div class="header__input-search">
            <input type="text" v-model="searchQuery" />
          </div>
          <div class="header__button-close">
            <button class="closePopup" @click="closePopup">x</button>
          </div>
        </slot>
      </div>
      <div class="popup-body">
        <slot name="body">
          <ul>
            <li v-for="(value, key) in searchCurrencies" :key="key">
              <span>{{ key }} - {{ value }}</span>
              <button @click="addCurrency(key)">Добавить</button>
            </li>
          </ul>
        </slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      required: true,
    },

    currencies: {
      type: Array,
      required: true,
    },
  },

  data: () => ({
    searchQuery: "",
    searchList: [],
  }),

  async created() {
    try {
      await fetch("https://openexchangerates.org/api/currencies.json")
        .then((response) => response.json())
        .then((data) => {
          console.log("data: ", data);
          this.searchList = data;
        })
        .catch((error) => {
          console.log("request API error: ", error);
        });
    } catch (error) {
      console.log(error);
    }
  },

  methods: {
    closePopup() {
      this.$emit("close");
    },

    addCurrency(currency) {
      this.$emit("addCurrency", currency);
    },
  },

  computed: {
    searchCurrencies() {
      return Object.keys(this.searchList)
        .filter((key) => {
          return this.searchList[key]
            .toLowerCase()
            .includes(this.searchQuery.toLowerCase());
        })
        .reduce((result, key) => {
          result[key] = this.searchList[key];
          return result;
        }, {});
    },
  },
};
</script>

<style lang="scss" src="./styles/style.scss">
</style>