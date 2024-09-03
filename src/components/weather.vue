<template>
  <div class="container">
        <div class="search-section">
            <input type="text" id="cityInput" class="custom-input" placeholder="Enter city name"  v-model="city" @keyup.enter="loadWeather">
            <div class="temperature-box">
                <h2>Weather Information</h2>
                <div class="weather-data"  v-if="weatherData !== null">
                    <span>Temperature:</span>
                    <span id="temperature">
                      {{ Math.round(weatherData.main.temp) }} °C
                    </span>
                    <span>Feels like:</span>
                    <span id="feels-like">
                      {{ Math.round(weatherData.main.feels_like) }} °C
                    </span>
                    <span>Min Temp:</span>
                    <span id="temp-min">
                      {{ Math.round(weatherData.main.temp_min) }} °C
                    </span>
                    <span>Max Temp:</span>
                    <span id="temp-max">
                      {{ Math.round(weatherData.main.temp_max) }} °C
                    </span>
                    <span>Humidity:</span>
                    <span id="humidity">
                      {{ Math.round(weatherData.main.humidity) }} %</span>
                </div>
            </div>
        </div>
        <div class="search-history">
            <h3>Recent Searches</h3>
            <ul v-for="city in arrayCitys">
                <li @click="loadSelected(city)" type="button" style="cursor: pointer">
                    {{ city }}
                </li>
            </ul>
        </div>
        <div id="warning">
          <h3 id="notificationTitle"></h3>
          <p id="notificationDescription"></p>
        </div>
    </div>
</template>
<script>
import Axios from 'axios'
export default {
  name: 'weather',
  data () {
      return {
        city: '',
        weatherData: null,
        WnbGeoLocation: 'Gornja Radgona',
        arrayCitys: [],
        api: 'https://api.openweathermap.org/data/2.5/weather?',
        apiKey: 'd63e80b492b53e401d8ced1572ce248b'
      }
    },
    mounted () {
      this.loadWeather()
    },
    methods: {
      loadWeather () {
        if (this.city === '') {
          this.city = this.WnbGeoLocation
        }
        Axios.get(this.api + '&q=' + this.city + '&appid=' + this.apiKey + '&units=metric')
        .then(response => {
          this.weatherData= null
          this.weatherData = response.data
          this.checkSearches(this.city)
        })
        .catch(err => {
          this.showNotification('Warning', err.response.data.message);
        })
    },
    loadSelected (cityName) {
      Axios.get(this.api + '&q=' + cityName + '&appid=' + this.apiKey + '&units=metric')
      .then(response => {
        this.city = cityName
        this.weatherData= null
        this.weatherData = response.data
        this.checkSearches(cityName)
      })
      .catch(err => {
        this.showNotification('Warning', err.response.data.message);
      })
    },
    checkSearches (city) {
      if (this.arrayCitys.length === 5) {
        this.arrayCitys.splice(2, 1)
        this.arrayCitys.push(city)
      } else {
        this.arrayCitys.push(city)
      }
    },
    showNotification(title, description) {
      const warning = document.getElementById('warning');
      document.getElementById('notificationTitle').textContent = title;
      document.getElementById('notificationDescription').textContent = description;
      warning.style.display = 'block';
      setTimeout(() => {
        warning.style.display = 'none';
      }, 3000);
    }
  }
}
</script>

<style>
#warning {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background-color: #f44336;
  color: white;
  padding: 15px;
  border-radius: 5px;
  display: none;
}

#warning h3 {
  margin: 0 0 10px 0;
}

#warning p {
  margin: 0;
}

.search-history li:hover {
  background-color: #e6f2ff;
}

body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 20px;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-color: #f0f4f8;
}

.container {
  display: flex;
  flex-direction: column;
  background-color: white;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  max-width: 800px;
  width: 100%;
  min-width: none;
}

.search-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 20px;
}

.custom-input {
  width: 100%;
  max-width: 300px;
  padding: 10px;
  font-size: 16px;
  border: 2px solid #ccc;
  border-radius: 5px;
  outline: none;
  transition: border-color 0.3s ease;
}

.custom-input:focus {
  border-color: #4a90e2;
}

.temperature-box {
  margin-top: 20px;
  padding: 20px;
  background-color: #e6f2ff;
  border-radius: 20px;
  text-align: left;
  width: 100%;
  max-width: 340px;
  min-width: 300px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.temperature-box h2 {
  margin-top: 0;
  text-align: center;
  margin-bottom: 15px;
}

.weather-data {
  display: grid;
  grid-template-columns: auto 1fr;
  gap: 10px;
  align-items: center;
}

.weather-data span:nth-child(odd) {
  font-weight: bold;
}

.search-history {
  width: 100%;
}

.search-history h3 {
  margin-top: 0;
}

.search-history ul {
  list-style-type: none;
  padding: 0;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}

.search-history li {
  margin-bottom: 10px;
  background-color: #f0f0f0;
  padding: 5px 10px;
  border-radius: 5px;
  flex-basis: calc(50% - 5px);
}

@media (min-width: 768px) {
  .container {
    flex-direction: row;
  }

  .search-section {
    margin-right: 20px;
    margin-bottom: 0;
  }

  .search-history {
    border-left: 1px solid #ccc;
    padding-left: 20px;
  }

  .search-history ul {
    flex-direction: column;
  }

  .search-history li {
    flex-basis: auto;
  }
}

@media only screen and (max-width: 500px) {
  .temperature-box {
    min-width: none;
  }

  .search-history ul {
    flex-wrap: none  !important;
    display: inline;
    justify-content: none;
  }
}
</style>
