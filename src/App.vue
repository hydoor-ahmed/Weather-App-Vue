<template>
  <div
    class="max-w-[375px] h-[667px] md:rounded-md overflow-hidden pt-6 md:shadow-md"
    :class="[
      weather != ''
        ? weather.weather[0].main == 'Clouds'
          ? 'cloud'
          : weather.weather[0].main == 'Clear'
          ? 'clear'
          : weather.weather[0].main == 'Sunny'
          ? 'sunny'
          : ''
        : '',
    ]"
  >
    <div
      v-if="pageLoaded != false"
      class="mx-auto w-[90%] px-2 py-1 flex items-center search rounded"
    >
      <i class="bx bx-search mr-2 text-slate-500"></i>
      <input
        @keypress="getWeather"
        v-model="query"
        type="text"
        placeholder="Search..."
        class="bg-transparent placeholder:text-sm"
      />
    </div>

    <div class="h-full" v-if="weather != ''">
      <div>
        <h1 class="mt-4 text-center text-white textShadow">{{ weather.weather[0].main }}</h1>
        <h1 class="text-center text-5xl mt-4 text-white textShadow">
          {{ Math.round(weather.main.temp) }}°
        </h1>
      </div>

      <img class="mt-48 mb-[-1px]" src="./assets/wave.svg" alt="wave img" />
      <div class="bg-white p-4 h-full">
        <h1
          class="text-center text-xl text-[#b5b8ff] uppercase tracking-widest"
        >
          {{ weather.name }}
        </h1>
        <p class="text-center mt-2 text-sm text-gray-400/75">
          {{ dateBuilder() }}
        </p>

        <ul class="mt-6">
          <li class="flex justify-between items-center border-b pb-2">
            <h1>Wind Speed</h1>
            <i class="bx bx-wind text-lg"></i>
            <h1>{{ Math.round(weather.wind.speed) }}</h1>
          </li>

          <li class="flex justify-between items-center mt-2 border-b pb-2">
            <h1>Humidity</h1>
            <img
              width="20"
              class="ml-6"
              src="./assets/humidity.png"
              alt="humidity img"
            />
            <h1>{{ Math.round(weather.main.humidity) }}</h1>
          </li>

          <li class="flex justify-between items-center mt-2">
            <h1>Pressure</h1>
            <img
              width="20"
              class="ml-10"
              src="./assets/pressure.png"
              alt="pressure img"
            />
            <h1>{{ weather.main.pressure }}</h1>
          </li>
        </ul>
      </div>
    </div>
    <div
      v-if="pageLoaded != true"
      class="h-full flex justify-center items-center"
    >
      <img src="./assets/loading.svg" width="150" alt="Loading..." />
    </div>
  </div>
</template>

<script>
import { ref } from "@vue/reactivity";
export default {
  setup() {
    let query = ref("Iraq");
    let weather = ref([]);
    let apiKey = "8371ce4027f36e98cb7172a5a838c1e0";
    let url = "https://api.openweathermap.org/data/2.5/";
    let pageLoaded = false;

    async function fetchWeather() {
      await fetch(`${url}weather?q=${query.value}&appid=${apiKey}&units=metric`)
        .then((res) => {
          return res.json();
        })
        .then((data) => (weather.value = data));
    }
    function getWeather(e) {
      if (e.key == "Enter" && query.value.trim() != "") {
        fetchWeather();
      }
    }

    function dateBuilder() {
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
    }

    return {
      query,
      weather,
      pageLoaded,
      getWeather,
      fetchWeather,
      dateBuilder,
    };
  },
  beforeMount() {
    setTimeout(() => {
      this.fetchWeather();
      this.pageLoaded = true;
    }, 800);
  },
};
</script>

<style>
.search {
  background: linear-gradient(0deg, #ffffffd5, #ffffff99);
  box-shadow: 1px 1px 1px 1px #7c7c7c3b;
}

.cloud {
  background-image: url(./assets/clouds-2.jpg);
  background-size: cover;
}
.clear {
  background-image: url(./assets/clear.png);
  background-size: cover;
}
.sunny {
  background-image: url(./assets/sunny.png);
  background-size: cover;
}

.textShadow {
  text-shadow: 1px 1px 2px #373737;
}

#app {
  @apply md:h-screen md:flex justify-center items-center;
}
</style>
