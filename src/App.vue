<template>
  <div
    id="app"
    :class="
      typeof weather.main != 'undefined' &&
      weather.main.temp > 22 &&
      weather.weather[0].main !== 'Rain'
        ? 'warm'
        : ''
    "
  >
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          v-model="query"
          placeholder="Pesquise..."
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main !== 'undefined'">
        <div class="location-box">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>
        <div class="weather-box">
          <div class="temperature">{{ Math.round(weather.main.temp) }}°c</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "App",
  data: () => ({
    api_key: "9fda6ce95c101bb3f89a7e3c8de37d45",
    url_base: "http://api.openweathermap.org/data/2.5/",
    query: "",
    standardQuery: "São Paulo",
    weather: {},
  }),

  mounted() {
    this.standardCity();
  },

  watch: {
    query() {
      if (this.query !== "") {
        this.debouce(this.query);
      }
    },
  },

  methods: {
    fetchWeather(val) {
      fetch(
        `${this.url_base}weather?q=${val}&units=metric&APPID=${this.api_key}`
      )
        .then((res) => {
          return res.json();
        })
        .then(this.setResults);
    },

    standardCity() {
      fetch(
        `${this.url_base}weather?q=${this.standardQuery}&units=metric&APPID=${this.api_key}`
      )
        .then((res) => {
          return res.json();
        })
        .then(this.setResults);
    },

    debouce(val) {
      clearTimeout(this.searchTimer);
      this.searchTimer = setTimeout(() => {
        this.fetchWeather(val);
      }, 500);
    },

    setResults(results) {
      this.weather = results;
      console.log(this.weather);
    },

    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,400;0,500;0,600;0,700;0,800;0,900;1,400;1,500;1,600;1,700;1,800;1,900&display=swap");

*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

#app {
  background-position: bottom;
  background-size: cover;
  background-image: url("./assets/cold-bg.jpg");
  font-family: "Montserrat", sans-serif;
  max-width: 500px;
  margin: 0 auto;
  transition: 0.4s;
}

#app.warm {
  background-image: url("./assets/warm-bg.jpg");
}

main {
  padding: 25px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
  min-height: 100vh;
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  appearance: none;
  background: none;
  background-color: rgba(255, 255, 255, 0.5);
  border: none;
  border-radius: 0 16px 0 16px;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
  color: #313131;
  display: block;
  font-size: 20px;
  outline: none;
  padding: 15px;
  transition: 0.4s;
  width: 100%;
}

.search-box .search-bar:focus {
  background-color: rgba(255, 255, 255, 0.75);
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  border-radius: 16px 0 16px 0;
}

.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-size: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temperature {
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  color: #fff;
  display: inline-block;
  font-size: 102px;
  font-weight: 900;
  margin: 30px 0;
  padding: 10px 25px;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
