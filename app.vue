<template>
  <div 
    v-if="errorMessage" 
    class="bg-rose-600 p-2 text-center text-white text-xl"
  >
    {{ errorMessage }}
  </div>

  <div class="container mx-auto py-10">
    <div class="flex items-center gap-2">
      <input 
        v-model="city"
        type="text" 
        class="px-4 py-1 border rounded-full outline-none" 
        placeholder="Город"
      >
      <button 
        type="button" 
        class="bg-indigo-500 text-white px-4 py-1 rounded-full"
        @click="changeWeather"
      >
        Получить данные
      </button>
    </div>

    <div class="mt-10">
      <div v-if="todayWeather">
        <div class="text-2xl font-bold my-2">Погода в городе/станице {{ weatherInfo.location.name }} на сегодня</div>
        <WeatherWidget :info="todayWeather" />
      </div>

      <div v-if="nextWeathers" class="flex flex-col gap-4 mt-10">
        <div class="text-2xl font-bold">Погода на ближайшие 3 дня</div>
        <NextWeatherWidget :weathers="nextWeathers" />
      </div>
    </div>
  </div>
</template>

<script setup>
  import WeatherWidget from './components/WeatherWidget.vue';
  import NextWeatherWidget from './components/NextWeatherWidget.vue';

  const apiKey = 'ff9a79d2ab5340ae8c5122125241902';

  const city = ref('');
  const weatherInfo = ref(null);
  const latitude = ref(0);
  const longitude = ref(0);
  const errorMessage = ref('');

  const todayWeather = computed(() => {
    return weatherInfo.value?.forecast?.forecastday?.[0].day;
  });

  const nextWeathers = computed(() => {
    const forecastdays = weatherInfo.value?.forecast?.forecastday;

    return forecastdays
      ?.slice(1, forecastdays?.length)
      ?.map(({ day, date }) => ({ ...day, date }));
  });

  const changeWeather = async () => {
    errorMessage.value = '';

    try {
      const { data, error } = await getWeather({
        city: city.value,
      });

      if (error.value) {
        errorMessage.value = error.value.data.error.message;
        return;
      }

      weatherInfo.value = data.value;
    } catch (error) {
      console.error(error)
    }
  }

  const getWeather = async ({ city, apiVersion = 'forecast' }) => {
    return useFetch(
      `http://api.weatherapi.com/v1/${apiVersion}.json?key=${apiKey}&q=${city}&lang=ru&days=4`
    );
  }

  onMounted(async () => {
    navigator.geolocation.getCurrentPosition(async (position) => {
      latitude.value = position.coords.latitude;
      longitude.value = position.coords.longitude;

      try {
        const { data, error } = await getWeather({
          city: [latitude.value, longitude.value].join(','),
        });

        if (error.value) {
          errorMessage.value = error.value.data.error.message;
          return;
        }

        weatherInfo.value = data.value;
        city.value = data.value.location.name;
      } catch (error) {
        console.error(error);
      }
    });
  });
</script>
