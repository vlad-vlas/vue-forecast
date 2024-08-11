<template>
  <div class="days-tab text-center">
    <div v-if="loading" class="loading">Loading..</div>
    <ul v-else class="d-flex">
      <li v-for="day in forecast" class="li_active" :key="day.date">
        <div class="py-2">{{ getDayWithMonth(day.date) }}</div>
        <div class="py-2">{{ getDayName(day.date) }}</div>
        <div class="py-2"><img :src="day.iconUrl" alt="" /></div>
        <div class="py-2">{{ day.temperature }}&deg;C</div>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";
import "moment/dist/locale/en-gb";

export default {
  props: {
    cityName: String,
  },
  data() {
    return {
      loading: true,
      forecast: [],
    };
  },
  mounted() {
    moment.locale("en-gb");
    this.fetchWeaterData();
  },
  methods: {
    async fetchWeaterData() {
      const api_key = "5d605117331fe4af7f7423fe0f25b392";
      const base_url = "https://api.openweathermap.org/data/2.5/";

      await axios
        .get(
          `${base_url}forecast?q=${this.cityName}&units=metric&appid=${api_key}`
        )
        .then((response) => {
          const forecastData = response.data.list;
          const filteredData = forecastData
            .map((i) => {
              return {
                date: moment(i.dt_txt.split(" ")[0]),
                temperature: Math.round(i.main.temp),
                description: i.weather[0].main,
                iconUrl: `https://api.openweathermap.org/img/w/${i.weather[0].icon}.png`,
              };
            })
            .reduce((acc, item) => {
              if (!acc.some((day) => day.date.isSame(item.date, "day"))) {
                acc.push(item);
              }
              return acc;
            }, [])
            .slice(1, 5);
          this.forecast = filteredData;
          this.loading = false;
        })
        .catch((error) => {
          console.error("Error featchig forecast data", error);
          this.loading = false;
        });
    },
    getDayName(date) {
      return date.format("dddd");
    },
    getDayWithMonth(date) {
      return date.format("D-MMM");
    },
  },
};
</script>

<style scoped>
@import url("../assets/css/forecast.css");
</style>
