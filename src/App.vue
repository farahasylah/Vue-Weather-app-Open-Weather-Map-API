<template>
  <!-- typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' : '',  -->
  <div id="app" class="" :class="darkmode == true ? 'dark':''" >
    <main>
      <div class="search-box">
        <LocationMarkerIcon class="h-4 w-4 text-grey-400"/>
        <input 
        v-model="query" 
        @keypress="fetchWeather"
        type="text" 
        class="search-bar border-2 rounded-full p-1" 
        placeholder="Search location.."/>
        <a class="modebtn hover:bg-black hover:text-white dark:text-white dark:border-white dark:hover:bg-white dark:hover:text-black" v-on:click="darkMode">
          <MoonIcon v-if="!darkmode" class="h-4 w-4 text-grey-400"/>
          <SunIcon v-else class="h-4 w-4 text-white-400"/>
          mode
        </a>
      </div>
      <br>
      <div class="weather-wrap text-center" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location text-center text-3xl dark:text-white">
            <LocationMarkerIcon class="h-6 w-6 text-purple-400"/>
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date italic dark:text-white">{{ dateBuilder() }}</div>
        </div>
        <div class="weather-box dark:bg-gray-200">
          <div class="weather-icon" >
            <div v-html="weatherIcon(weather.weather[0].main)"></div>
            <div class="weather"> {{ weather.weather[0].main }} </div>
          </div>
          <div class="temp text-6xl">{{ Math.round(weather.main.temp) }}°C</div>
        </div>

      </div>
      
    </main>
  </div>

</template>

<script>
import './assets/index.css'
import { LocationMarkerIcon,MoonIcon,SunIcon } from '@heroicons/vue/outline'
export default {
  name: 'app',
  mounted() {  
    document.title = 'Vue Weather app using Open Weather Map API and dark mode';  
  }, 
  data() {
    return {
      api_key: '207a1e6a9123b10b4ae0a07ca97f6692',
      url_base: 'http://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      darkmode: false,
    }
  },
  methods :{
    fetchWeather ( e ) {
      if ( e.key == "Enter" ){
        fetch( `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}` )
          .then( response => {
              return response.json();   
          }).then( this.weatherResult );
      }
    },
    weatherResult (result) {
      this.weather = result;
    },
    dateBuilder () {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"];
      
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      
      return `${day}, ${date} ${month} ${year}`;
    },
    weatherIcon (weather) {
      let clouds = '<svg class="h-24 w-24 text-purple-500"  width="24" height="24" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" fill="none" stroke-linecap="round" stroke-linejoin="round">  <path stroke="none" d="M0 0h24v24H0z"/>  <path d="M7 18a4.6 4.4 0 0 1 0 -9h0a5 4.5 0 0 1 11 2h1a3.5 3.5 0 0 1 0 7h-12" /></svg>';
      let clear = '<svg class="h-24 w-24 text-purple-500"  viewBox="0 0 24 24"  fill="none"  stroke="currentColor"  stroke-width="2"  stroke-linecap="round"  stroke-linejoin="round">  <circle cx="12" cy="12" r="5" />  <line x1="12" y1="1" x2="12" y2="3" />  <line x1="12" y1="21" x2="12" y2="23" />  <line x1="4.22" y1="4.22" x2="5.64" y2="5.64" />  <line x1="18.36" y1="18.36" x2="19.78" y2="19.78" />  <line x1="1" y1="12" x2="3" y2="12" />  <line x1="21" y1="12" x2="23" y2="12" />  <line x1="4.22" y1="19.78" x2="5.64" y2="18.36" />  <line x1="18.36" y1="5.64" x2="19.78" y2="4.22" /></svg>';
      let snow = '<svg class="h-24 w-24 text-purple-500"  viewBox="0 0 24 24"  fill="none"  stroke="currentColor"  stroke-width="2"  stroke-linecap="round"  stroke-linejoin="round">  <path d="M20 17.58A5 5 0 0 0 18 8h-1.26A8 8 0 1 0 4 16.25" />  <line x1="8" y1="16" x2="8.01" y2="16" />  <line x1="8" y1="20" x2="8.01" y2="20" />  <line x1="12" y1="18" x2="12.01" y2="18" />  <line x1="12" y1="22" x2="12.01" y2="22" />  <line x1="16" y1="16" x2="16.01" y2="16" />  <line x1="16" y1="20" x2="16.01" y2="20" /></svg>';
      let mist = '<svg class="h-24 w-24 text-purple-500"  width="24" height="24" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" fill="none" stroke-linecap="round" stroke-linejoin="round">  <path stroke="none" d="M0 0h24v24H0z"/>  <path d="M5 5h3m4 0h9" />  <path d="M3 10h11m4 0h1" />  <path d="M5 15h5m4 0h7" />  <path d="M3 20h9m4 0h3" /></svg>';
      let wind = '<svg class="h-24 w-24 text-purple-500"  viewBox="0 0 24 24"  fill="none"  stroke="currentColor"  stroke-width="2"  stroke-linecap="round"  stroke-linejoin="round">  <path d="M9.59 4.59A2 2 0 1 1 11 8H2m10.59 11.41A2 2 0 1 0 14 16H2m15.73-8.27A2.5 2.5 0 1 1 19.5 12H2" /></svg>';
      if(weather == 'Clouds')
        return clouds;
      else if(weather == 'Clear')
        return clear;
      else if(weather == 'Snow')
        return snow;
      else if(weather == 'Mist')
        return mist;
      else if(weather == 'Wind')
        return wind;
      else
        return weather;
    },
    darkMode(){
      this.darkmode = !this.darkmode;
    },

  },
  components: { LocationMarkerIcon,MoonIcon,SunIcon }
  
}
</script>
