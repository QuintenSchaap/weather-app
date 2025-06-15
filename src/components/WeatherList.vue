<template>
  <div class="container py-2">

    <form @submit.prevent="addCity" class="mb-2 d-flex gap-2">
      <input v-model="newCity" class="form-control" placeholder="Enter a city" />
      <button class="btn btn-primary">Add City</button>
    </form>

    <div v-for="(city, index) in cities" :key="city.name" class="card mb-2">
      <div class="card-body">
        <div class="d-flex justify-content-between align-items-center">
          <h5 class="card-title">{{ city.name }}</h5>
          <button class="btn btn-sm btn-outline-danger" @click="removeCity(index)">Remove</button>
        </div>

        <div v-if="city.weatherToday && city.weatherTomorrow">
          <strong>Today:</strong> {{ city.weatherToday.temp }}°C, {{ city.weatherToday.description }}<br>
          <strong>Tomorrow:</strong> {{ city.weatherTomorrow.temp }}°C, {{ city.weatherTomorrow.description }}
        </div>
        <div v-else>
          <p>Loading weather data...</p>
        </div>

        <div v-if="city.error" class="text-danger mt-2">{{ city.error }}</div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      apiKey: '9c78ede3cb3b425b2a6fa98dc884352a',
      newCity: '',
      cities: [],
    };
  },
  mounted() {
    this.addCityByName('Amsterdam');
  },
  methods: {
    async addCity() {
      if (this.newCity.trim() !== '') {
        await this.addCityByName(this.newCity.trim());
        this.newCity = '';
      }
    },
    async addCityByName(name) {
      const cityObj = {
        name,
        weatherToday: null,
        weatherTomorrow: null,
        error: null,
      };
      this.cities.push(cityObj);

      try {
        const res = await axios.get(
            `https://api.openweathermap.org/data/2.5/forecast?q=${name}&units=metric&appid=${this.apiKey}`
        );

        const list = res.data.list;
        const now = new Date();
        const today = now.getDate();
        const tomorrow = new Date(now);
        tomorrow.setDate(today + 1);
        const tomorrowDate = tomorrow.getDate();

        const todayForecast = list.find((item) => {
          return new Date(item.dt_txt).getDate() === today;
        });

        const tomorrowForecast = list.find((item) => {
          const date = new Date(item.dt_txt);
          return date.getDate() === tomorrowDate && date.getHours() === 12;
        });

        cityObj.weatherToday = {
          temp: todayForecast.main.temp,
          description: todayForecast.weather[0].description,
        };

        cityObj.weatherTomorrow = {
          temp: tomorrowForecast.main.temp,
          description: tomorrowForecast.weather[0].description,
        };
      } catch (err) {
        cityObj.error = 'Error loading weather for ' + name;
        console.error(err);
      }
    },
    removeCity(index) {
      this.cities.splice(index, 1);
    },
  },
};
</script>