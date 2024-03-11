<script>
  let city = '';
  let currentWeather = {};
  let hourlyForecast = [];

  async function getWeather() {
    const apikey = "51a99c71696ca7540d3559de6c7a22d8";

    if (!city) {
      alert('Please enter a city');
      return;
    }

    const currentWeatherUrl = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apikey}`;
    const forecastUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&appid=${apikey}`;

    try {
      const currentWeatherResponse = await fetch(currentWeatherUrl);
      const currentWeatherData = await currentWeatherResponse.json();
      currentWeather = currentWeatherData;
    } catch (error) {
      console.error('Error fetching current weather data:', error);
      alert('Error fetching current weather data. Please try again.');
    }
    
    try {
      const forecastResponse = await fetch(forecastUrl);
      const forecastData = await forecastResponse.json();
      hourlyForecast = forecastData.list.slice(0, 8); // Display the next 8 items (3-hour intervals)
    } catch (error) {
      console.error('Error fetching hourly forecast data:', error);
      alert('Error fetching hourly forecast data. Please try again.');
    }
  }

  function displayHourlyForecast(hourlyData) {
    return hourlyData.map(item => {
      const dateTime = new Date(item.dt * 1000);
      const hour = dateTime.getHours();
      const temperature = Math.round(item.main.temp - 273.15);
      const iconCode = item.weather[0].icon;
      const iconUrl = `https://openweathermap.org/img/wn/${iconCode}.png`;

      return {
        hour,
        temperature,
        iconUrl
      };
    });
  }
</script>

<main>
  <form on:submit|preventDefault={getWeather} class="displayform">
    <h1>Weather App</h1>

  <label for="city">Enter City:</label>
  <input type="text" bind:value={city} id="city" />

  <button on:click={getWeather}>Get Weather</button>
  </form>
  
<div class="card">
  {#if Object.keys(currentWeather).length > 0}
  
      <p>{Math.round(currentWeather.main.temp - 273.15)}°C</p>
  

    <p class="weather-info" >
      <p>{currentWeather.name}</p>
      <p>{currentWeather.weather[0].description}</p>
    

    <img src={`https://openweathermap.org/img/wn/${currentWeather.weather[0].icon}.png`} alt="Weather Icon" />

    <p class ="hourly-forecast" >
      {#each displayHourlyForecast(hourlyForecast) as { hour, temperature, iconUrl }}
        <p class="hourly-item">
          <span>{hour}:00</span>
          <img src={iconUrl} alt="Hourly Weather Icon" />
          <span>{temperature}°C</span>
        </p>
      {/each}
</p>
  {:else}
    
      <p>No weather data available</p>
 

  {/if}
</div>
</main>

<style>
  main {
    background: #8C52FF;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100vh;
    margin: 0;
  }
  .displayform {
		text-align: center;
		margin-top: 20px;
	}
  .card {
    background-color: rgba(255, 255, 255, 0.3);
    padding: 20px;
    border-radius: 15px;
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.1);
    text-align: center; 
    color: #fff;
    margin-top: 20px;
  }
  
  h1 {
    font-size: 32px;
    margin-bottom: 20px;
    color: #fff;
  }
  
  label {
    display: block;
    margin-bottom: 10px;
    font-size: 16px;
    color: #fff;
  }
  
  input {
    width: calc(100% - 16px);
    padding: 8px;
    box-sizing: border-box;
    border-radius: 10px;
    border: 1px solid white;
    margin-top: 20px;
  }
  
  button {
    background: #debff4;
    color: white;
    padding: 10px;
    border: none;
    border-radius: 10px;
    cursor: pointer;
    margin-top: 20px;
    width: 100px;
    font-size: 15px;
  }
  
  button:hover {
    background: #8b48d7;
  }
  .weather-info {
    font-size: 20px;
  }
  
  .hourly-forecast {
    
    align-items: center;
    margin-top: 20px;
  }
  
  .hourly-item {
    width: 80px;
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom: 10px; 
    color: white;
  }
  
  .hourly-item img {
    width: 30px;
    height: 30px;
    margin-bottom: 5px;
  }
  </style>
  