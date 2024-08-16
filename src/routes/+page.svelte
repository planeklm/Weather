<script>
  import Icon from "@iconify/svelte";
  import { fade, fly, slide, crossfade } from "svelte/transition";
  import { quintIn, quintOut, sineOut } from "svelte/easing";
  import { onMount } from "svelte";
  import { writable } from "svelte/store";

  const darkMode = writable(true);

  /** @type {{ name: any; sys: { country: any; sunrise: number; sunset: number; }; main: { temp: number; humidity: any; }; weather: { description: any; }[]; wind: { speed: any; }; timezone: number; } | null} */
  let weather = null;
  let city = "";
  let cityPlaceholder = "City";
  let weatherIcon = "";
  let apiKey = "df53165254f089e92287cdfa813d5368";
  let loading = false;
  let clicked = false;
  /** @type {null} */
  let error = null;

  const websiteTitle = "Weather";
  const websiteDescription =
    "A modern weather search made in Svelte + tailwind, utilizing the OpenWeather api.";
  const imageUrl = "https://files.catbox.moe/4uk5s9.png";
  const themeColor = "#000000";

  const countryCapitals = {
    AE: "Abu Dhabi",
    AG: "Saint John's",
    AR: "Buenos Aires",
    AS: "Pago Pago",
    AT: "Vienna",
    AU: "Canberra",
    BB: "Bridgetown",
    BE: "Brussels",
    BG: "Sofia",
    BO: "La Paz",
    BR: "Brasília",
    BS: "Nassau",
    BY: "Minsk",
    BZ: "Belmopan",
    CA: "Ottawa",
    CH: "Bern",
    CL: "Santiago",
    CN: "Beijing",
    CO: "Bogotá",
    CR: "San José",
    CY: "Nicosia",
    CZ: "Prague",
    DE: "Berlin",
    DK: "Copenhagen",
    DM: "Roseau",
    DO: "Santo Domingo",
    DZ: "Algiers",
    EC: "Quito",
    EE: "Tallinn",
    EG: "Cairo",
    ES: "Madrid",
    ET: "Addis Ababa",
    FI: "Helsinki",
    FJ: "Suva",
    FM: "Palikir",
    FR: "Paris",
    GB: "London",
    GD: "St. George's",
    GH: "Accra",
    GR: "Athens",
    GU: "Hagåtña",
    HN: "Tegucigalpa",
    HR: "Zagreb",
    HT: "Port-au-Prince",
    HU: "Budapest",
    ID: "Jakarta",
    IE: "Dublin",
    IL: "Jerusalem",
    IN: "New Delhi",
    IS: "Reykjavik",
    IT: "Rome",
    JM: "Kingston",
    JP: "Tokyo",
    KE: "Nairobi",
    KI: "Tarawa",
    KR: "Seoul",
    KY: "George Town",
    KZ: "Nur-Sultan",
    LC: "Castries",
    LT: "Vilnius",
    LU: "Luxembourg",
    LV: "Riga",
    MA: "Rabat",
    MD: "Chișinău",
    MH: "Majuro",
    MP: "Saipan",
    MT: "Valletta",
    MX: "Mexico City",
    MY: "Kuala Lumpur",
    NG: "Abuja",
    NI: "Managua",
    NL: "Amsterdam",
    NO: "Oslo",
    NR: "Yaren",
    NU: "Alofi",
    NZ: "Wellington",
    PA: "Panama City",
    PE: "Lima",
    PG: "Port Moresby",
    PH: "Manila",
    PL: "Warsaw",
    PN: "Adamstown",
    PR: "San Juan",
    PT: "Lisbon",
    PW: "Ngerulmud",
    PY: "Asunción",
    RO: "Bucharest",
    RU: "Moscow",
    SA: "Riyadh",
    SB: "Honiara",
    SE: "Stockholm",
    SG: "Singapore",
    SI: "Ljubljana",
    SK: "Bratislava",
    SV: "San Salvador",
    TH: "Bangkok",
    TK: "Fakaofo",
    TO: "Nuku'alofa",
    TT: "Port of Spain",
    TV: "Funafuti",
    TZ: "Dodoma",
    UA: "Kyiv",
    UG: "Kampala",
    US: "Washington, D.C.",
    VC: "Kingstown",
    VE: "Caracas",
    VN: "Hanoi",
    VU: "Port Vila",
    WS: "Apia",
    ZA: "Pretoria",
  };

  const countries = Object.keys(countryCapitals);
  let selectedCountry = countries[0];

  $: {
    cityPlaceholder = countryCapitals[selectedCountry] || "City";
  }

  function isDaytime(sunrise, sunset, current) {
    return current >= sunrise && current <= sunset;
  }

  async function fetchWeather() {
    error = null;
    loading = true;
    try {
      const response = await fetch(
        `https://api.openweathermap.org/data/2.5/weather?q=${city || cityPlaceholder},${selectedCountry}&units=metric&appid=${apiKey}`
      );
      if (!response.ok) throw new Error("Failed to fetch weather data");
      clicked = true;
      weather = await response.json();

      const currentTime = Math.floor(Date.now() / 1000) + weather.timezone;
      const daytime = isDaytime(
        weather.sys.sunrise,
        weather.sys.sunset,
        currentTime
      );
      darkMode.set(!daytime);
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

  function toggleTheme() {
    darkMode.set(!$darkMode);
  }
</script>

<div class:dark={$darkMode}>
  <div
    class="flex flex-col sm:flex-row min-h-screen w-full items-center justify-center space-y-5 sm:space-y-0 sm:space-x-5 bg-gray-100 dark:bg-[#070707] transition-colors duration-200 p-4"
  >
    <h1
      class="sm:hidden bg-gradient-to-t from-[#404040] via-[#ffffff] to-[#ffffff] inline-block text-transparent bg-clip-text text-center font-bold font-title text-2xl sm:text-6xl pl-5 pr-5"
      in:fade={{ delay: 100, duration: 250 }}
    >
      Weather on mobile is in beta.
    </h1>

    <div
      class="w-full max-w-[450px] sm:w-[450px] h-auto sm:h-[325px] border border-gray-300 dark:border-[#252525] rounded-3xl bg-white dark:bg-[#111111] flex flex-col items-center justify-between py-6 transition-colors duration-200"
    >
      <div class="text-center">
        <Icon
          icon="material-symbols:umbrella-rounded"
          class="text-gray-800 dark:text-[#ffffff] text-8xl mx-auto block"
        />
        <h1
          class="text-gray-800 dark:text-[#ffffff] text-[17.5px] font-sans mt-4"
        >
          Select your city and country to view the weather
        </h1>
        <h1
          class="text-gray-600 dark:text-[#999999] text-[15px] font-sans mt-2 px-8"
        >
          Your request will be sent to OpenWeather to receive the current
          weather
        </h1>
      </div>
      <form
        on:submit|preventDefault={fetchWeather}
        class="flex flex-col sm:flex-row space-y-2 sm:space-y-0 sm:space-x-2 pb-5 px-4 sm:px-0 w-full sm:w-auto"
      >
        <input
          bind:value={city}
          type="text"
          placeholder={cityPlaceholder}
          class="flex font-sans px-4 py-2 w-full sm:w-[200px] bg-gray-50 dark:bg-[#070707] border border-gray-300 dark:border-[#252525] rounded-md focus:outline-none placeholder-gray-500 dark:placeholder-[#a2a2a2] text-gray-900 dark:text-white transition-colors duration-200"
        />
        <select
          bind:value={selectedCountry}
          id="country"
          name="country"
          class="flex font-sans px-2 py-3 w-full sm:w-auto bg-gray-50 dark:bg-[#070707] border border-gray-300 dark:border-[#252525] rounded-md focus:outline-none text-gray-900 dark:text-white transition-colors duration-200"
        >
          {#each countries as country}
            <option value={country}>{country}</option>
          {/each}
        </select>
        <button
          type="submit"
          class="px-4 py-2 w-full sm:w-auto font-sans bg-blue-500 dark:bg-white text-white dark:text-black rounded-md hover:bg-blue-600 dark:hover:bg-[#c4c4c4] transition-colors duration-200"
        >
          Search
        </button>
      </form>
    </div>
    {#if clicked}
      <div
        class="w-full max-w-[250px] sm:w-[250px] h-auto sm:h-[325px] border border-gray-300 dark:border-[#252525] rounded-3xl bg-white dark:bg-[#111111] flex flex-col items-center justify-between py-6 transition-colors duration-200"
        in:slide={{ delay: 200, duration: 500, axis: "x" }}
      >
        {#if loading}
          <p
            class="text-center text-gray-600 dark:text-gray-400"
            in:fly={{ delay: 200, duration: 200 }}
          >
            Loading weather data...
          </p>
        {:else if error}
          <p
            class="text-center text-red-500 dark:text-red-200 text-lg mx-auto p-5"
            in:fly={{ duration: 200 }}
          >
            <Icon
              icon="material-symbols:error-outline-rounded"
              class="text-red-500 dark:text-red-200 text-8xl mx-auto"
            />
            {error}. Please try again.
          </p>
        {:else if weather}
          <h2
            class="text-xl font-semibold mb-2 text-gray-800 dark:text-white"
            in:fly={{ delay: 500, duration: 200 }}
          >
            {weather.name}, {weather.sys.country}
          </h2>
          <p
            class="text-4xl font-bold mb-2 text-gray-800 dark:text-white"
            in:fly={{ delay: 500, duration: 200 }}
          >
            {Math.round(weather.main.temp)}°C
          </p>
          <div in:fly={{ delay: 500, duration: 200 }}>
            <Icon
              icon={weatherIcon}
              class="text-gray-800 dark:text-[#ffffff] text-8xl mx-auto block"
            />
          </div>
          <div
            class="mt-4 grid grid-cols-2 gap-2"
            in:fly={{ delay: 500, duration: 200 }}
          >
            <div>
              <p class="text-sm text-gray-600 dark:text-white">Humidity</p>
              <p class="font-semibold text-gray-800 dark:text-white">
                {weather.main.humidity}%
              </p>
            </div>
            <div>
              <p class="text-sm text-gray-600 dark:text-white">Wind Speed</p>
              <p class="font-semibold text-gray-800 dark:text-white">
                {weather.wind.speed} m/s
              </p>
            </div>
          </div>
          <div class="mt-2 text-sm text-gray-600 dark:text-gray-400">
            {$darkMode ? "Night" : "Day"} time
          </div>
        {/if}
      </div>
    {/if}
    <button
      on:click={toggleTheme}
      class="fixed top-4 right-4 p-2 rounded-full bg-gray-200 dark:bg-[#252525] text-gray-800 dark:text-white transition-colors duration-200"
    >
      <Icon
        icon={$darkMode ? "ph:sun-bold" : "ph:moon-bold"}
        class="text-2xl"
      />
    </button>
  </div>
</div>

<svelte:head>
  <title>{websiteTitle}</title>
  <meta property="og:title" content={websiteTitle} />
  <meta property="og:description" content={websiteDescription} />
  <meta property="og:image" content={imageUrl} />
  <meta name="theme-color" content={themeColor} />
  <meta name="twitter:card" content="summary_large_image" />
</svelte:head>

<style lang="postcss">
  :global(html) {
    @apply bg-gray-100 dark:bg-[#070707];
  }
  :global(.dark) {
    color-scheme: dark;
  }
</style>
