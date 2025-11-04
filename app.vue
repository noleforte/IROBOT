<template>
  <div>
    <NuxtPage />
  </div>
</template>

<style>
/* Глобальные стили для скрытия scrollbar и черного фона */
html {
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* IE и Edge */
  overflow: -moz-scrollbars-none; /* Старые версии Firefox */
}

html::-webkit-scrollbar {
  display: none; /* Chrome, Safari, Opera */
}

body {
  margin: 0;
  padding: 0;
  background: #000;
  overflow-x: hidden;
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* IE и Edge */
}

body::-webkit-scrollbar {
  display: none; /* Chrome, Safari, Opera */
}

* {
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* IE и Edge */
}

*::-webkit-scrollbar {
  display: none; /* Chrome, Safari, Opera */
}
</style>
