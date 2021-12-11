<svelte:options tag="custom-element" />

<script>
  import { initialData } from "./data";
  import { getWeather } from "./stores/weatherStore";

  export let city = "Pula";
  export let units = "metric";

  let url;
  let description;
  let weatherIcon;
  let icon;
  let temp;
  let feelsLike;
  let pressure;
  let humidity;
  let windSpeed;
  let windDirection;
  let windDirectionConverted;

  async function initWeather() {
    url = initialData.host + "?q=" + city + "&appid=" + initialData.key + "&units=" + units;
    const data = await getWeather(url).then((data) => {
      console.log(data);
      description = data.weather[0].description;
      weatherIcon = data.weather[0].icon;
      icon = initialData.weatherIconURL + weatherIcon + initialData.weatherIconExtension;
      temp = toOneDecimal(data.main.temp);
      feelsLike = toOneDecimal(data.main.feels_like);
      pressure = data.main.pressure;
      humidity = data.main.humidity;
      windSpeed = toOneDecimal(data.wind.speed);
      windDirection = data.wind.deg;
      convertWindDirection(windDirection);
    });
  }

  function toOneDecimal(num) {
    num = Math.round (num * 10) / 10
    return num;
  }
  function changeUnits() {
    if (units === "metric") {
      units = "imperial";
    } else if (units === "imperial") {
      units = "metric";
    }
    initWeather();
  }

  function convertWindDirection(direction) {
    console.log(windDirection);
    switch (true) {
      case direction >= 337.5 && direction < 22.5:
        windDirectionConverted = "N";
        break;
      case direction >= 22.5 && direction < 67.5:
        windDirectionConverted = "NE";
        break;
      case direction >= 67.5 && direction < 112.5:
        windDirectionConverted = "E";
        break;
      case direction >= 112.5 && direction < 157.5:
        windDirectionConverted = "SE";
        break;
      case direction >= 157.5 && direction < 202.5:
        windDirectionConverted = "S";
        break;
      case direction >= 202.5 && direction < 247.5:
        windDirectionConverted = "SW";
        break;
      case direction >= 247.5 && direction < 292.5:
        windDirectionConverted = "W";
        break;
      case direction >= 292.5 && direction < 337.5:
        windDirectionConverted = "NW";
        break;
      default:
        windDirectionConverted = direction;
    }
    console.log(windDirectionConverted);
  }
</script>

<svelte:head>
  <style>
    @import url("fonts/FjallaOne-Regular.ttf");
  </style>
</svelte:head>

<div class="wrapper">
  {#await initWeather()}
    <p class="loading">Loading...</p>
  {:then}
    <header>
      <h1 class="title">{city}, {description}</h1>
      <img class="icon" src={icon} alt="" />
    </header>
    <main>
      <div class="temperature">
        <p class="temp-number">{temp}</p>
        {#if units === "metric"}
          <p class="temp-unit temp-unit-metric" on:click={changeUnits}>O</p>
        {:else if units === "imperial"}
          <p class="temp-unit temp-unit-imperial" on:click={changeUnits}>F</p>
        {/if}
      </div>
      <div class="weather-data">
        <p>
          Feels like: {feelsLike}
          {#if units === "metric"}
            <span> C</span>
          {:else}
            <span> F</span>
          {/if}
        </p>
        <p>Pressure: {pressure} hPa</p>
        <p>Humidity: {humidity} %</p>
        <p>
          Wind: {windSpeed}
          {#if units === "metric"}
            <span> km/h</span>
          {:else}
            <span> mph</span>
          {/if}
          <span>{windDirectionConverted}</span>
        </p>
      </div>
    </main>
  {/await}
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
  }
  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .title {
    color: $color-text;
  }
  .icon {
    max-width: 50px;
    max-height: 50px;
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
</style>
