<template>
  <div class="container py-2">
    <h2>Weather in Amsterdam</h2>

    <div v-if="weatherToday && weatherTomorrow">
      <h3>Today</h3>
      <p>Temperature: {{ weatherToday.temp }} °C</p>
      <p>Condition: {{ weatherToday.description }}</p>

      <h3>Tomorrow</h3>
      <p>Temperature: {{ weatherTomorrow.temp }} °C</p>
      <p>Condition: {{ weatherTomorrow.description }}</p>
    </div>

    <div v-else>
      <p>Loading weather data...</p>
    </div>

    <div v-if="error" class="text-danger mt-3">{{ error }}</div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      weatherToday: null,
      weatherTomorrow: null,
      error: null,
      apiKey: '9c78ede3cb3b425b2a6fa98dc884352a',
    };
  },
  mounted() {
    this.fetchAmsterdamWeather();
  },
  methods: {
    async fetchAmsterdamWeather() {
      this.error = null;
      try {
        const city = 'Amsterdam';
        const res = await axios.get(
            `https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${this.apiKey}`
        );

        const list = res.data.list;
        const now = new Date();
        const today = now.getDate();
        const tomorrow = new Date(now);
        tomorrow.setDate(today + 1);
        const tomorrowDate = tomorrow.getDate();

        const todayForecast = list.find(item => {
          return new Date(item.dt_txt).getDate() === today;
        });

        const tomorrowForecast = list.find(item => {
          const date = new Date(item.dt_txt);
          return date.getDate() === tomorrowDate && date.getHours() === 12;
        });

        this.weatherToday = {
          temp: todayForecast.main.temp,
          description: todayForecast.weather[0].description,
        };

        this.weatherTomorrow = {
          temp: tomorrowForecast.main.temp,
          description: tomorrowForecast.weather[0].description,
        };
      } catch (err) {
        this.error = 'Error fetching weather data.';
        console.error(err);
      }
    },
  },
};
</script>