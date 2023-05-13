<script>
	import axios from 'axios';
	import Icon from '@iconify/svelte';

	const api_key = 'cdf39c4f743e454f81644754231205';
	const weather_endpoint = 'current.json'
	const forecast_endpoint = 'forecast.json'
	const BASE_URL_WEATHER = `http://api.weatherapi.com/v1/${weather_endpoint}?key=${api_key}&q=`;
	const BASE_URL_FORECAST = `http://api.weatherapi.com/v1/${forecast_endpoint}?key=${api_key}&q=`;

	let city = '', weatherData = {}, errorFlag = false, errorMessage = "Please Enter a valid city name!";

	// Convert Unix timestamp to local time
	const formatTime = (epoch) => {
		const date = new Date(epoch * 1000);
		return date.toLocaleTimeString();
	}


	const handleSubmit = async (e) => {
		errorFlag = false;
		if(!city.trim().length) {
			return;
		}
		try {
			const response = await axios.get(BASE_URL_WEATHER+`${city}`);	
			if(response.status === 200) {
				const weather_data = response.data.current;
				const location_data = response.data.location;
				weatherData = {
					feelslike_c: weather_data.feelslike_c,
					feelslike_f:weather_data.feelslike_f,
					gust_kph: weather_data.gust_kph,
					gust_mph:weather_data.gust_mph,
					humidity:weather_data.humidity,
					is_day:weather_data.is_day,
					last_updated:weather_data.last_updated,
					last_updated_epoch:weather_data.last_updated_epoch,
					precip_in:weather_data.precip_in,
					precip_mm:weather_data.precip_mm,
					pressure_in: weather_data.pressure_in,
					pressure_mb: weather_data.pressure_mb,
					temp_c: weather_data.temp_c,
					temp_f: weather_data.temp_f,
					uv: weather_data.uv,
					vis_km: weather_data.vis_km,
					vis_miles: weather_data.vis_miles,
					wind_degree:weather_data.wind_degree,
					wind_dir:weather_data.wind_dir,
					wind_kph: weather_data.wind_kph,
					wind_mph: weather_data.wind_mph,
					condition: {
						text: weather_data.condition.text,
						icon: weather_data.condition.icon,
						code: weather_data.condition.code
					},
					country: location_data.country,
					lat: location_data.lat,
					localtime: location_data.localtime,
					localtime_epoch: location_data.localtime_epoch,
					lon: location_data.lon,
					name: location_data.name,
					region: location_data.region,
					tz_id: location_data.tz_id
				}
			}
		} catch(err) {
			console.error("Entered value is not a valid city name...")
			errorFlag = true;
		}
		city = '';
	}

	const handleReset = (e) => {
		city = '', weatherData = {}, errorFlag = false;
	}

</script>

<div class="input-container">
	<div class="text-box">
		<input type="text" bind:value={city} placeholder="Enter city name" />
		<button on:click={(e) => handleSubmit(e)}>Get Weather</button>
		<button on:click={(e) => handleReset(e)}>Reset</button>
	</div>
</div>
{#if weatherData?.is_day}
	<div class="weather-card">
		<div class="location">
		<h2>{weatherData.region}, {weatherData.country}</h2>
		<p class="last-updated">Last updated: {formatTime(weatherData.last_updated_epoch)}</p>
		</div>
		<div class="weather-info">
		<div class="temperature">
			<h3>{weatherData.temp_c}&deg;C</h3>
			<p class="condition">{weatherData.condition.text}</p>
		</div>
		<div class="details">
			<div>
			<img class="icon" src={weatherData.condition.icon} alt="Weather Icon" />
			<div class="info-parent">
				<Icon icon="ph:thermometer" color="currentColor" width="20px" />
				<p class="feels-like">Feels like: {weatherData.feelslike_c}&deg;C</p>
			</div>
			</div>
			<div>
			<div class="info-parent"><Icon icon="ph:wind" color="currentColor" width="20px" /><p class="info">Wind: {weatherData.wind_kph} km/h</p></div>
			<div class="info-parent">
				<Icon icon="mdi:car-brake-low-pressure" color="currentColor" width="20px" />
				<p class="info">Pressure: {weatherData.pressure_mb} mb</p>
			</div> 
			<div class="info-parent">
				<Icon icon="streamline:interface-weather-rain-2-cloud-rain-rainy-meteorology-precipitation-weather" color="currentColor" width="20px" />
				<p class="info">Precipitation: {weatherData.precip_mm} mm</p>
			</div>
			</div>
		</div>
		</div>
	</div> 
{/if}

{#if errorFlag}
<div class="error-card">
	<p class="error-message">{errorMessage}</p>
</div>
{/if}

<style>
	.input-container {
		display: flex;
		margin-top: 50px;
	}
	.text-box {		
		margin: 0 auto; 
	}

	input {
		padding: 5px;
		margin-right: 10px;
	}

	button {
		padding: 5px 10px;
	}
	.weather-card {
		border-radius: 10px;
		padding: 16px;
		background-color: #fff;
		box-shadow: 2px 4px 6px rgba(0, 0, 0, 0.2);
		width: 500px;
		margin: 0 auto; 
		margin-top: 50px;
  }

  .location {
    margin-bottom: 16px;
  }

  .location h2 {
    font-size: 24px;
    margin-bottom: 8px;
  }

  .last-updated {
    color: #777;
    font-size: 14px;
  }

  .weather-info {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .temperature {
    text-align: center;
  }

  .temperature h3 {
    font-size: 48px;
    font-weight: bold;
    margin-bottom: 8px;
  }

  .condition {
    color: #555;
    font-size: 18px;
    margin-top: 4px;
  }

  .details {
    display: flex;
    align-items: center;
  }

  .details div {
    margin-right: 24px;
  }

  .icon {
    width: 50px;
    height: 50px;
    margin-bottom: 8px;
  }

  .info {
    color: #555;
    font-size: 16px;
    margin-bottom: 8px;
	margin-left: 4px;
	padding-bottom: 6px;
  }

  .info-parent {
	display: flex;
	align-items: center;
  }

  .feels-like {
	margin-left: 4px;
  }

  .error-card {
    border-radius: 10px;
    padding: 16px;
    background-color: #fde2e2;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
	width: 500px;
	margin: 0 auto; 
	margin-top: 50px;
  }

  .error-message {
    color: #e53e3e;
    font-size: 16px;
    font-weight: bold;
    text-align: center;
  }
</style>
