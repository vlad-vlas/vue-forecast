<template>
  <div class="app">
    <div class="header container h-100 p-5">
      <h1 class="mb-5">{{ title }}</h1>
      <div class="d-flex justify-content-center h-100">
        <div class="searchbar w-25 mx-5">
          <input
            type="text"
            class="input form-control text-white border-0 bg-transparent"
            v-model="city"
            placeholder="City name"
          />
          <datalist class="searchbox" id="list">
            <option v-for="(city, i) in dataList" :value="city" :key="i" />
          </datalist>
        </div>
        <button class="btn btn-search btn-primary" @click="searchWeather">
          Search <i class="fas fa-search"></i>
        </button>
      </div>
    </div>
    <br />
    <TheWeather :city="city" v-if="showWeather" />
  </div>
</template>

<script>
import TheWeather from "./components/TheWeather.vue";

export default {
  name: "App",
  components: {
    TheWeather,
  },

  data() {
    return {
      city: "",
      showWeather: false,
      title: "Weather forecast",
    };
  },
  methods: {
    async searchWeather() {
      this.showWeather = false;
      await this.$nextTick();
      this.showWeather = true;
    },
  },
};
</script>

<style>
@import url("./assets/css/main.css");
</style>
