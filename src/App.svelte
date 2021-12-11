<svelte:options tag="custom-element" />

<script>
  import { initialData } from "./data";
  import { getWeather } from "./stores/weatherStore";

  export let city = "Pula";
  export let units = "metric";

  $: urlWeather = initialData.host + initialData.requestWeather + "?q=" + city + "&appid=" + initialData.key + "&units=" + units;
  $: urlForecast = initialData.host + initialData.requestForecast + "?q=" + city + "&appid=" + initialData.key + "&units=" + units;

  let isForecastOpen = false;

  //openweathermap gives us forecast for every 3 hours from when we fetch the data.
  //To get 24h intervals forecast => 24/3 = 8, array starts from 0 so we have 24h intervals at list item 7,15,23,31,39
  const forecastListItems = [7, 15, 23, 31, 39];

  function timeConverter(UNIX_timestamp) {
    let a = new Date(UNIX_timestamp * 1000);
    let days = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
    let dayNum = new Date(UNIX_timestamp * 1000).getDay();
    let dayOfWeek = days[dayNum];
    /* let months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"];
    let year = a.getFullYear();
    let month = months[a.getMonth()];
    let date = a.getDate();
    let hour = a.getHours();
    let min = a.getMinutes();
    let sec = a.getSeconds();
    let time = date + " " + month + " " + year + " " + hour + ":" + min + ":" + sec; */
    return dayOfWeek;
  }

  function toOneDecimal(num) {
    num = Math.round(num * 10) / 10;
    return num;
  }
  function changeUnits() {
    if (units === "metric") {
      units = "imperial";
    } else if (units === "imperial") {
      units = "metric";
    }
  }

  function convertWindDirection(direction) {
    switch (true) {
      case direction >= 337.5 && direction < 22.5:
        return "N";
        break;
      case direction >= 22.5 && direction < 67.5:
        return "NE";
        break;
      case direction >= 67.5 && direction < 112.5:
        return "E";
        break;
      case direction >= 112.5 && direction < 157.5:
        return "SE";
        break;
      case direction >= 157.5 && direction < 202.5:
        return "S";
        break;
      case direction >= 202.5 && direction < 247.5:
        return "SW";
        break;
      case direction >= 247.5 && direction < 292.5:
        return "W";
        break;
      case direction >= 292.5 && direction < 337.5:
        return "NW";
        break;
      default:
        return direction;
    }
  }

  let isForecastWindowOpen = false;
  function handleOpenForecast() {
    getWeather(urlForecast);
    isForecastWindowOpen = !isForecastWindowOpen;
    console.log(isForecastWindowOpen);
  }
</script>

<svelte:head>
  <style>
    @import url("fonts/FjallaOne-Regular.ttf");
  </style>
</svelte:head>

<div class="wrapper">
  {#await getWeather(urlWeather)}
    <p class="loading">Loading...</p>
  {:then data}
    <header>
      <h1 class="title">{data.name}, {data.weather[0].description}</h1>
      <img class="icon" src={initialData.weatherIconURL + data.weather[0].icon + initialData.weatherIconExtension} alt="" />
    </header>
    <main>
      <div class="temperature">
        <p class="temp-number">{toOneDecimal(data.main.temp)}</p>
        {#if units === "metric"}
          <p class="temp-unit temp-unit-metric" on:click={changeUnits}>O</p>
        {:else if units === "imperial"}
          <p class="temp-unit temp-unit-imperial" on:click={changeUnits}>F</p>
        {/if}
      </div>
      <div class="weather-data">
        <p>
          Feels like: {toOneDecimal(data.main.feels_like)}
          {#if units === "metric"}
            <span> C</span>
          {:else}
            <span> F</span>
          {/if}
        </p>
        <p>Pressure: {data.main.pressure} hPa</p>
        <p>Humidity: {data.main.humidity} %</p>
        <p>
          Wind: {toOneDecimal(data.wind.speed)}
          {#if units === "metric"}
            <span> km/h</span>
          {:else}
            <span> mph</span>
          {/if}
          <span>{convertWindDirection(data.wind.deg)}</span>
        </p>
      </div>
    </main>
  {/await}

  <details on:click={handleOpenForecast}>
    <!--  -->
    <summary />
    {#if isForecastWindowOpen}
      <div class="forecast-container">
        <p class="forecast-title">5 Day Forecast</p>
        <div class="forecast-days">
          {#await getWeather(urlForecast)}
            <p>Loading forecast...</p>
          {:then data}
            {#each forecastListItems as day}
              <div class="forecast-day">
                <p class="forecast-day-data">{timeConverter(data.list[day].dt)}</p>
                <p class="forecast-day-data">{toOneDecimal(data.list[day].main.temp)}</p>
                <p class="forecast-day-data">{data.list[day].weather[0].main}</p>
              </div>
            {/each}
          {:catch error}
            <p>{error.message}</p>
          {/await}
        </div>
      </div>
    {/if}
  </details>
</div>

<style lang="scss">
  $color-primary: #09095b;
  $color-text: white;

  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  p {
    color: white;
  }
  .loading {
    font-size: 32px;
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .wrapper {
    background: $color-primary;
    border: 2px solid white;
    border-radius: 18px;
    box-shadow: 0 2px 2px 2px rgba(0, 0, 0, 0.3);
    padding: 15px;
    width: 100%;
    max-width: 400px;
  }
  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .title {
    color: $color-text;
    font-size: 14px;
  }
  .icon {
    max-width: 50px;
    max-height: 50px;
  }
  main {
    display: flex;
    justify-content: space-between;
  }
  .temperature {
    display: flex;
    justify-content: center;
    align-items: flex-start;
  }
  .temp-number {
    font-size: 45px;
    line-height: 1;
  }
  .temp-unit {
    cursor: pointer;
    font-size: 25px;
    line-height: 1;
    &::after {
      opacity: 0.3;
      position: absolute;
    }
  }
  .temp-unit-metric {
    &:hover {
      &::after {
        content: "F";
      }
    }
  }
  .temp-unit-imperial {
    &:hover {
      &::after {
        content: "O";
      }
    }
  }
  .forecast-container {
    border: 2px solid white;
    border-radius: 4px;
    padding: 15px 0;
  }
  .forecast-title {
    line-height: 1;
    padding: 0 3px 15px 3px;
  }
  .forecast-days {
    display: flex;
    justify-content: space-between;
    padding: 0 12px;
  }
  .forecast-day-data {
    padding-bottom: 15px;
  }
  details > summary {
    list-style: none;
  }
  details > summary::-webkit-details-marker {
    display: none;
  }
  summary {
    width: 100px;
    height: 50px;
    background: white;
    &::marker {
      display: none;
    }
  }
</style>
