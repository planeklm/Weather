<script>
  import Icon from "@iconify/svelte";
  import { fade, fly, slide, crossfade } from "svelte/transition";
  import { quintIn, quintOut, sineOut } from "svelte/easing";
  import { onMount } from "svelte";

  /**
   * @type {{ name: any; sys: { country: any; }; main: { temp: number; humidity: any; }; weather: { description: any; }[]; wind: { speed: any; }; } | null}
   */
  let weather = null;
  let city = "";
  let weatherIcon = "";
  let apiKey = "df53165254f089e92287cdfa813d5368";
  let loading = false;
  let clicked = false;
  /**
   * @type {null}
   */
  let error = null;

  const countries = [
    "US",
    "CA",
    "EU",
    "GB",
    "FR",
    "DE",
    "IT",
    "ES",
    "JP",
    "CN",
    "IN",
    "BR",
    "AU",
    "MX",
    "RU",
    "ZA",
    "KR",
    "SE",
    "NO",
    "FI",
    "DK",
    "PL",
    "NL",
    "BE",
    "CH",
    "AT",
    "IE",
    "PT",
    "GR",
    "CZ",
    "HU",
    "SK",
    "RO",
    "BG",
    "HR",
    "SI",
    "LT",
    "LV",
    "EE",
    "IS",
    "MT",
    "CY",
    "LU",
    "MD",
    "UA",
    "BY",
    "KZ",
    "TH",
    "SG",
    "MY",
    "PH",
    "VN",
    "ID",
    "AE",
    "SA",
    "IL",
    "NG",
    "KE",
    "TZ",
    "EG",
    "MA",
    "DZ",
    "GH",
    "ET",
    "UG",
    "JM",
    "TT",
    "CL",
    "AR",
    "CO",
    "PE",
    "PY",
    "VE",
    "BO",
    "EC",
    "SV",
    "HN",
    "NI",
    "CR",
    "PA",
    "DO",
    "HT",
    "JM",
    "AG",
    "BB",
    "BS",
    "BZ",
    "LC",
    "GD",
    "DM",
    "VC",
    "TT",
    "KY",
    "PR",
    "AS",
    "GU",
    "MP",
    "FM",
    "MH",
    "PW",
    "TV",
    "WS",
    "TO",
    "VU",
    "CK",
    "NU",
    "TK",
    "NF",
    "PN",
    "MH",
    "FM",
    "KI",
    "MH",
    "NR",
    "TV",
    "SB",
    "VU",
    "PG",
    "FJ",
    "TO",
    "WS",
    "AS",
    "CK",
    "NU",
    "TK",
    "NF",
    "PN",
  ];

  let selectedCountry = countries[0];

  async function fetchWeather() {
    error = null;
    try {
      const response = await fetch(
        `https://api.openweathermap.org/data/2.5/weather?q=${city},${selectedCountry}&units=metric&appid=${apiKey}`
      );
      if (city == "") return;
      else if (!response.ok) throw new Error("Failed to fetch weather data");
      clicked = true;
      weather = await response.json();
    } catch (e) {
      // @ts-ignore
      error = e.message;
    } finally {
      fetchWeatherIcon();
      loading = false;
    }
  }

  function fetchWeatherIcon() {
    if (weather == null) {
      return;
    }
    const description = weather.weather[0].description.toLowerCase();
    console.log(description);
    if (description.includes("cloud")) {
      weatherIcon = "material-symbols:cloudy"; // Cloudy icon
    } else if (description.includes("clear")) {
      weatherIcon = "material-symbols:sunny-rounded"; // Sunny icon
    } else if (description.includes("rain")) {
      weatherIcon = "material-symbols:rainy-rounded"; // Rainy icon
    } else {
      weatherIcon = "material-symbols:emoticon-rounded"; // Default to cloudy if no match
    }
  }
</script>

<div class="flex sm:hidden items-center justify-center min-h-screen w-full">
  <div class="text-center">
    <h1
      class="bg-gradient-to-t from-[#404040] via-[#ffffff] to-[#ffffff] inline-block text-transparent bg-clip-text font-bold font-title text-6xl sm:text-6xl pl-5 pr-5 pb-5"
      in:fade={{ delay: 100, duration: 250 }}
    >
      🖥️
    </h1>
    <h1
      class="bg-gradient-to-t from-[#404040] via-[#ffffff] to-[#ffffff] inline-block text-transparent bg-clip-text font-bold font-title text-3xl sm:text-6xl pl-5 pr-5"
      in:fade={{ delay: 100, duration: 250 }}
    >
      Weather is not available on mobile devices
    </h1>
    <h1
      class="text-[#606060] inline-block font-bold font-title text-xl pl-5 pr-5"
      in:fade={{ delay: 100, duration: 250 }}
    >
      Please view on a larger display.
    </h1>
  </div>
</div>

<div>
  <div
    class="flex-1 hidden sm:flex h-screen w-full items-center justify-center space-x-5"
  >
    <div
      class="w-[450px] h-[325px] border border-[#252525] rounded-3xl bg-[#111111] flex flex-col items-center justify-between py-6"
    >
      <div class="text-center">
        <Icon
          icon="material-symbols:umbrella-rounded"
          class="text-[#ffffff] text-8xl mx-auto block"
        />
        <h1 class="text-[#ffffff] text-[17.5px] font-sans mt-4">
          Select your city and country to view the weather
        </h1>
        <h1 class="text-[#999999] text-[15px] font-sans mt-2 px-8">
          Your request will be sent to OpenWeather to recieve the current
          weather
        </h1>
      </div>

      <form on:submit|preventDefault={fetchWeather} class="flex space-x-2 pb-5">
        <input
          bind:value={city}
          type="text"
          placeholder="Las Vegas"
          class="flex font-sans px-4 py-2 w-[200px] bg-[#070707] border border-[#252525] rounded-md focus:outline-none placeholder-[#a2a2a2] text-white transition ease-in-out"
        />
        <select
          bind:value={selectedCountry}
          id="country"
          name="country"
          class="flex font-sans px-2 py-3 bg-[#070707] border border-[#252525] rounded-md focus:outline-none placeholder-[#a2a2a2] text-white transition ease-in-out"
        >
          {#each countries as country}
            <option value={country}>{country}</option>
          {/each}
        </select>
        <button
          type="submit"
          class="px-4 py-2 font-sans bg-white text-black rounded-md hover:bg-[#c4c4c4] transition ease-in-out"
        >
          Search
        </button>
      </form>
    </div>
    {#if clicked}
      <div
        class="w-[250px] h-[325px] border border-[#252525] rounded-3xl bg-[#111111] flex flex-col items-center justify-between py-6"
        in:slide={{ delay: 200, duration: 500, axis: "x" }}
      >
        {#if loading}
          <p
            class="text-center text-gray-600"
            in:fly={{ delay: 200, duration: 200 }}
          >
            Loading weather data...
          </p>
        {:else if error}
          <p
            class="text-center text-red-200 text-lg mx-auto p-5"
            in:fly={{ duration: 200 }}
          >
            <Icon
              icon="material-symbols:error-outline-rounded"
              class="text-red-200 text-8xl mx-auto"
            />
            {error}. Please try again.
          </p>
        {:else if weather}
          <h2
            class="text-xl font-semibold mb-2 text-white"
            in:fly={{ delay: 500, duration: 200 }}
          >
            {weather.name}, {weather.sys.country}
          </h2>
          <p
            class="text-4xl font-bold mb-2 text-white"
            in:fly={{ delay: 500, duration: 200 }}
          >
            {Math.round(weather.main.temp)}°C
          </p>
          <div in:fly={{ delay: 500, duration: 200 }}>
            <Icon
              icon={weatherIcon}
              class="text-[#ffffff] text-8xl mx-auto block"
            />
          </div>
          <div
            class="mt-4 grid grid-cols-2 gap-2"
            in:fly={{ delay: 500, duration: 200 }}
          >
            <div>
              <p class="text-sm text-white">Humidity</p>
              <p class="font-semibold text-white">
                {weather.main.humidity}%
              </p>
            </div>
            <div>
              <p class="text-sm text-white">Wind Speed</p>
              <p class="font-semibold text-white">
                {weather.wind.speed} m/s
              </p>
            </div>
          </div>
        {/if}
      </div>
    {/if}
  </div>
</div>

<style lang="postcss">
  :global(html) {
    background-color: #070707;
  }
</style>
