<template>
  <div class="flex gap-4">
    <WeatherWidget 
      v-for="weather, index in weathers" 
      :key="index"
      :info="weather" 
    />
  </div>
</template>

<script setup>
  import WeatherWidget from './WeatherWidget.vue';

  defineProps({
    weathers: {
      type: Array,
      required: true,
    }
  })
</script>