<template>
  <div class="flex flex-col gap-4">
    <div class="text-xl font-semibold">{{ info.date }}</div>

    <div class="flex flex-col gap-4 border-2 rounded-2xl p-4 w-96">  
      <div class="flex items-center gap-2">
        <img :src="info.condition.icon" alt class="h-20 w-20">
        <div class="text-xl font-semibold">{{ info.condition.text }}</div>
      </div>

      <div>Температура: {{ info.avgtemp_c }}°C</div>
      <div>Влажность: {{ info.avghumidity }}%</div>
      <div>Скорость ветра: {{ ((info.maxwind_kph * 1000) / 3600).toFixed(2) }} м/с</div>
    </div>
  </div>
</template>

<script setup>
  defineProps({
    info: {
      type: Object,
      required: true,
    },
  })
</script>