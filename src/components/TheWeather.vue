<template>
  <div class="container p-0">
    <div class="cards d-flex-wrap" v-if="success">
      <div>
        <h2 class="mb-1 day text-center">Today's forecast</h2>
      </div>
      <div class="card today main-div w-50">
        <div class="p-2 d-flex-wrap">
          <div class="place-local d-flex-wrap text-center">
            <h2 class="place">
              <i class="fa fa-location"
                >{{ name }} <small>{{ country }}</small></i
              >
            </h2>
            <div class="data-time d-flex">
              <p class="text-center date d-flex mb-0">{{ localDate }}</p>
              <small class="times">{{ localTime }}</small>
            </div>
          </div>
          <div class="temp">
            <h2 class="weather-temp">{{ temperature }}&deg;</h2>
            <h2 class="text-light">
              {{ description }} <img :src="iconUrl" :alt="description" />
            </h2>
            <div id="app" :class="temp < 18 ? 'cold' : 'warm'"></div>
          </div>
          <div class="humidity-wind">
            <span class="humidity">Humidity: {{ humidity }}%</span>
            <span class="wind">Wind: {{ Math.round(wind) }} m/s</span>
          </div>
        </div>
      </div>

      <div class="card-2 w-100">
        <h2 class="text-center my-3 forecast">Forecast for 4 days</h2>
        <Forecast :cityName="city" />
        <div id="div_form" class="d-flex my-3 justify-content-center">
          <form action="">
            <input
              type="buton"
              value="Change city"
              class="btn change_btn btn-outline-secondary"
              @click="changeLocatiob"
            />
          </form>
        </div>
      </div>
    </div>

    <div v-else-if="loading" class="text-center" style="color: white">
      <h4>Loading...</h4>
    </div>
    <div v-else class="text-center" style="color: white">
      <h4 class="message">Error: city not found</h4>
    </div>
    <form class="reboot text-center" action="">
      <input
        type="buton"
        value="Update"
        class="btn reboot_btn btn-outline-secondary"
        @click="changeLocatiob"
      />
    </form>
  </div>
</template>

<script>
import Forecast from "./Forecast.vue";
import axios from "axios";
import moment from "moment";
import "moment/dist/locale/en-gb";

export default {
  components: {
    Forecast,
  },
  props: {
    city: String,
  },
  data() {
    return {
      success: false,
      loading: true,
      name: "",
      country: "",
      temperature: null,
      description: null,
      iconUrl: null,
      localDate: null,
      localTime: null,
      wind: null,
      humidity: null,
    };
  },
  async created() {
    this.loading = true;
    const api_key = "5d605117331fe4af7f7423fe0f25b392";
    const base_url = "https://api.openweathermap.org/data/2.5/";
    const weatherData = await axios
      .get(`${base_url}weather?q=${this.city}&units=metric&APPID=${api_key}`)
      .then((r) => {
        this.success = true;
        this.loading = false;
        return r.data;
      })
      .catch((error) => {
        console.error("Error featchig data", error);
        this.success = false;
        this.loading = false;
      });

    this.name = weatherData.name;
    this.country = weatherData.sys.country;
    this.temperature = Math.round(weatherData.main.temp);
    this.description = weatherData.weather[0].main;
    this.wind = weatherData.wind.speed;
    this.humidity = weatherData.main.humidity;

    this.iconUrl = `https://api.openweathermap.org/img/w/${weatherData.weather[0].icon}.png`;

    const now = moment(new Date());
    moment.locale("en-gb");
    this.localDate = now.format("dddd DD MMMM YYYY");
    this.localTime = now.format("HH:mm:ss");
  },
  methods: {
    changeLocatiob() {
      window.location.reload();
    },
  },
};
</script>

<style scoped>
@import url("../assets/css/weather.css");
</style>
