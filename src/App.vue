<template>
  <div id="app">
    <main>
      <div class="search-box">
        <input 
          type="text" 
          class="search-bar" 
          placeholder="Search..."
          v-model="query" 
          @keypress="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="weather.main != undefined">
        <div class="location-box">
          <div class="location"> {{weather.name}}, {{weather.sys.country}} </div>
          <div class="date"> {{ dateBuilder() }} </div>
        </div>

        <div class="weather-box">
          <div class="temp"> {{Math.round(weather.main.temp)}}°C </div>
          <div class="humidity"> {{weather.main.humidity}} % </div>
          <div class="pressure"> {{weather.main.pressure}} hPa </div>
          <div class="weather"> {{weather.weather[0].main}} </div>
          <div class="weatherImg"> 
            <img v-bind:src="imgSrc" />
          </div>
        </div>

    </div>
    <div class="weather-wrap" v-else-if="error !== ''">
        <h2>{{error}} </h2>
    </div>
    </main>
  </div>
</template>

<script>


export default {
  name: 'App',
  data () {
    return {
      api_key_weather: '84dff302558be91ca0e5cbe998a8c827',
      url:"https://api.openweathermap.org/data/2.5/",
      query: '',
      weather: {},
      error: '',
      imgSrc: ''
    }
  },
  methods: {
    fetchWeather(e){
      console.log('FETCH')
      if(e.key =="Enter"){
        fetch(`${this.url}weather?q=${this.query}&units=metric&APPID=${this.api_key_weather}`)
          .then(res => {
            return res.json();
          }).then(this.setResults);
      }
    },
    setResults(results) {
      console.log(results)
      console.log(results.message)
      if (results.cod == "404") {
        this.error = results.message
        this.weather = {}
      } else {
        this.weather = results;
        this.error = ''; 
        this.imgSrc = `https://openweathermap.org/img/wn/${results.weather[0].icon}@2x.png`
      }
    },
    dateBuilder() {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July",
      "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`
    }
  },
  created: function() {
    //Default options onLoad for location - Brno
    fetch(`${this.url}weather?q=Brno&units=metric&APPID=${this.api_key_weather}`)
      .then(res => {
        return res.json();
      }).then(this.setResults);
  }
  }

</script>

<style>

@import url('https://fonts.googleapis.com/css2?family=Teko&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  }

  body {
    font-family: 'Teko', sans-serif;
  }

  main {
    min-height: 100vh;
    padding: 25px;
  }

  .weather-wrap {
    border-radius: 15px;
    width: 40%;
    margin: 0 auto;
    -webkit-box-shadow: 0px 0px 60px 8px rgba(0,0,0,0.75);
    -moz-box-shadow: 0px 0px 60px 8px rgba(0,0,0,0.75);
    box-shadow: 0px 0px 60px 8px rgba(0,0,0,0.75);
  }

  .search-box {
    width: 40%;
    margin:5px auto;
  }

  .search-box .search-bar {
    display: block;
    width: 100%;
    padding: 15px;
    font-size: 20px;
    appearance: none;
    outline: none;
    background-color: rgba(255, 255, 255, 0.5);
    transition: 0.4s;
    border: none;
  }

  .location-box .location {
    color: black;
    font-size: 35px;
    font-weight: 500;
    text-transform: uppercase;
    text-align: center;
    text-shadow: 1px 3px rgba(0,0,0,0.25);
  }

  .location-box .date {
    color: black;
    font-size: 25px;
    font-weight: 300;
    font-style: italic;
    text-align: center;
  }

  .weather-box {
    text-align: center;
  }

  .weather-box .temp {
    display: inline-block;
    padding: 10px 20px;
    color: black;
    font-size: 102px;
    font-weight: 900;
    background-color: rgba(255, 255, 255, 0.5);
    border-radius: 16px;
    margin: 30px 0px;
  }

  .weather-box .weather {
    color: black;
    font-size: 48px;
    font-weight: 700;
    font-style: italic;
    text-shadow: 3px 6px rgba(0,0,0,0.25);
  }

  .weather-box .humidity,
  .weather-box .pressure {
    font-size: 25px;
    margin: 5px;
    font-weight: 500;
  }
 
 .weather-wrap h2 {
   text-align: center;
 }

</style>
