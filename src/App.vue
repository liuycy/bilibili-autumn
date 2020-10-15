<template>
  <bili-banner></bili-banner>
  <h1 style="text-align: center">哔哩哔哩 (゜-゜)つロ 干杯~-bilibili</h1>
  <img class="full-bg" src="/src/assets/full-bg.png" />
</template>

<script>
import BiliBanner from './components/BiliBanner.vue'

export default {
  name: 'App',
  components: { BiliBanner },
}
</script>

<style>
body {
  margin: 0;
}
</style>

<style scoped>
.full-bg {
  min-width: 999px;
  width: 100vw;
}
</style>
