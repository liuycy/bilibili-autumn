<template>
  <div ref="placeholder" class="banner">
    <img class="banner-placeholder" src="/src/assets/full-bg.png" />
    <canvas :ref="(el) => (layers.bg = el)" class="banner-layer"></canvas>
    <canvas :ref="(el) => (layers.twotwo = el)" class="banner-layer"></canvas>
    <canvas :ref="(el) => (layers.land = el)" class="banner-layer"></canvas>
    <canvas :ref="(el) => (layers.ground = el)" class="banner-layer"></canvas>
    <canvas :ref="(el) => (layers.grass = el)" class="banner-layer"></canvas>
    <canvas
      :ref="(el) => (layers.threethree = el)"
      class="banner-layer"
    ></canvas>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, onBeforeMount, watch, nextTick } from 'vue'

const images = reactive({
  bg: null,
  twotwo: null,
  land: null,
  ground: null,
  grass: null,
  threethree: null,
})
function buildImage(src) {
  return new Promise((resolve) => {
    const image = new Image()
    image.onload = () => resolve(image)
    image.src = src
  })
}
onBeforeMount(() => {
  buildImage('/src/assets/bg.png').then((img) => (images.bg = img))
  buildImage('/src/assets/22-open-eye.png').then((img) => (images.twotwo = img))
  buildImage('/src/assets/land.png').then((img) => (images.land = img))
  buildImage('/src/assets/ground.png').then((img) => (images.ground = img))
  buildImage('/src/assets/grass.png').then((img) => (images.grass = img))
  buildImage('/src/assets/33.png').then((img) => (images.threethree = img))
})

export const placeholder = ref(null)

export const layers = reactive({
  bg: null,
  twotwo: null,
  land: null,
  ground: null,
  grass: null,
  threethree: null,
})
function draw(image, config) {
  const { sx = 0, sy = 0, sw, sh, blur: b } = config || {}
  return {
    to(canvas) {
      const ctx = canvas.getContext('2d')
      ctx.filter = `blur(${b}px)`
      ctx.drawImage(image, sx, sy, sw, sh, 0, 0, canvas.width, canvas.height)
    },
  }
}
function resize(layers) {
  return {
    with(placeholder) {
      const { width, height } = placeholder.value.getBoundingClientRect()
      for (const canvas of Object.values(layers)) {
        canvas.width = width
        canvas.height = height
      }
    },
  }
}
onMounted(async () => {
  resize(layers).with(placeholder)

  window.addEventListener('resize', () => {
    resize(layers).with(placeholder)
    images.bg && draw(images.bg, { sw: 3000, sh: 250, blur: 6 }).to(layers.bg)
    images.twotwo &&
      draw(images.twotwo, { sw: 3000, sh: 275, blur: 0 }).to(layers.twotwo)
    images.land &&
      draw(images.land, { sw: 3000, sh: 250, blur: 5 }).to(layers.land)
    images.ground &&
      draw(images.ground, { sx: -100, sw: 3000, sh: 250, blur: 4 }).to(
        layers.ground
      )
    images.grass &&
      draw(images.grass, { sx: 150, sw: 3000, sh: 275, blur: 3 }).to(
        layers.grass
      )
    images.threethree &&
      draw(images.threethree, { sw: 3000, sh: 275, blur: 2 }).to(
        layers.threethree
      )
  })

  watch(
    () => images.bg,
    () => draw(images.bg, { sw: 3000, sh: 250, blur: 6 }).to(layers.bg)
  )

  watch(
    () => images.twotwo,
    () => draw(images.twotwo, { sw: 3000, sh: 275, blur: 0 }).to(layers.twotwo)
  )

  watch(
    () => images.land,
    () => draw(images.land, { sw: 3000, sh: 250, blur: 5 }).to(layers.land)
  )

  watch(
    () => images.ground,
    () =>
      draw(images.ground, { sx: -100, sw: 3000, sh: 250, blur: 4 }).to(
        layers.ground
      )
  )

  watch(
    () => images.grass,
    () =>
      draw(images.grass, { sx: 150, sw: 3000, sh: 275, blur: 3 }).to(
        layers.grass
      )
  )

  watch(
    () => images.threethree,
    () =>
      draw(images.threethree, { sw: 3000, sh: 275, blur: 2 }).to(
        layers.threethree
      )
  )
})
</script>

<style lang="scss" scoped>
.banner {
  display: flex;
  width: 100vw;
  min-width: 999px;
  position: relative;

  &-placeholder {
    width: 100%;
    height: 9.375vw;
    min-height: 95px;
    opacity: 0.3;
  }

  &-layer {
    position: absolute;
    top: 0;
    left: 0;
  }
}
</style>
