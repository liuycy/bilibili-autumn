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

import bg from '/src/assets/bg.png'
import twotwo from '/src/assets/22-open-eye.png'
import twotwoCloseEye from '/src/assets/22-close-eye.png'
import twotwoClosingEye from '/src/assets/22-closing-eye.png'
import twotwoOpeningEye from '/src/assets/22-opening-eye.png'
import land from '/src/assets/land.png'
import ground from '/src/assets/ground.png'
import grass from '/src/assets/grass.png'
import threethree from '/src/assets/33.png'

const images = reactive({
  bg: null,
  twotwo: null,
  twotwoCloseEye: null,
  twotwoClosingEye: null,
  twotwoOpeningEye: null,
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
  buildImage(bg).then((img) => (images.bg = img))
  buildImage(twotwo).then((img) => (images.twotwo = img))
  buildImage(twotwoCloseEye).then((img) => (images.twotwoCloseEye = img))
  buildImage(twotwoClosingEye).then((img) => (images.twotwoClosingEye = img))
  buildImage(twotwoOpeningEye).then((img) => (images.twotwoOpeningEye = img))
  buildImage(land).then((img) => (images.land = img))
  buildImage(ground).then((img) => (images.ground = img))
  buildImage(grass).then((img) => (images.grass = img))
  buildImage(threethree).then((img) => (images.threethree = img))
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
const config = {
  bg: { sw: 3000, sh: 250, blur: 6 },
  twotwo: { sw: 3000, sh: 275, blur: 1 },
  land: { sw: 3000, sh: 250, blur: 5 },
  ground: { sx: -100, sw: 3000, sh: 250, blur: 4 },
  grass: { sx: 150, sw: 3000, sh: 275, blur: 3 },
  threethree: { sw: 3000, sh: 275, blur: 2 },
}
function draw(image, config) {
  const { sx = 0, sy = 0, sw, sh, blur: b } = config || {}
  return {
    to(canvas) {
      const ctx = canvas.getContext('2d')
      ctx.imageSmoothingEnabled = true
      ctx.clearRect(0, 0, canvas.width, canvas.height)
      ctx.filter = `blur(${b}px)`
      ctx.drawImage(image, sx, sy, sw, sh, 0, 0, canvas.width, canvas.height)
    },
  }
}
function resize(layers) {
  return {
    with({ width, height }) {
      for (const canvas of Object.values(layers)) {
        canvas.width = width
        canvas.height = height
      }
    },
  }
}
onMounted(async () => {
  resize(layers).with(placeholder.value.getBoundingClientRect())

  window.addEventListener('resize', () => {
    resize(layers).with(placeholder.value.getBoundingClientRect())
    images.bg && draw(images.bg, config.bg).to(layers.bg)
    images.twotwo && draw(images.twotwo, config.twotwo).to(layers.twotwo)
    images.land && draw(images.land, config.land).to(layers.land)
    images.ground && draw(images.ground, config.ground).to(layers.ground)
    images.grass && draw(images.grass, config.grass).to(layers.grass)
    images.threethree &&
      draw(images.threethree, config.threethree).to(layers.threethree)
  })

  setTimeout(wink, 4800)
  async function wink() {
    await new Promise((r) => setTimeout(r, 50))
    images.twotwoClosingEye &&
      draw(images.twotwoClosingEye, config.twotwo).to(layers.twotwo)
    await new Promise((r) => setTimeout(r, 50))
    images.twotwoCloseEye &&
      draw(images.twotwoCloseEye, config.twotwo).to(layers.twotwo)
    await new Promise((r) => setTimeout(r, 50))
    images.twotwoOpeningEye &&
      draw(images.twotwoOpeningEye, config.twotwo).to(layers.twotwo)
    await new Promise((r) => setTimeout(r, 50))
    images.twotwo && draw(images.twotwo, config.twotwo).to(layers.twotwo)
    setTimeout(wink, 4800)
  }

  function render(ratio) {
    if (ratio < 0 && images.bg) {
      let c = { ...config.bg }
      c.blur = c.blur + ratio * c.blur
      draw(images.bg, c).to(layers.bg)
    }

    if (images.twotwo) {
      let c = config.twotwo
      if (ratio > 0) {
        c.blur = 1 + 2 * ratio
      } else {
        c.blur = 1 + 1 * ratio
      }
      draw(images.twotwo, c).to(layers.twotwo)
    }

    if (images.land) {
      let c = { ...config.land }
      let rb = ratio * c.blur
      if (rb < 2) {
        c.blur = c.blur - (rb / 2) * c.blur + 1
      } else {
        c.blur = rb - 2
      }

      c.sx = (c.sx || 0) - ratio * 15
      draw(images.land, c).to(layers.land)
    }

    if (images.ground) {
      let c = { ...config.ground }
      let rb = ratio * c.blur
      if (rb < 2) {
        c.blur = c.blur - (rb / 2) * c.blur + 1
      } else {
        c.blur = rb - 2
      }

      c.sx = (c.sx || 0) - ratio * 20
      draw(images.ground, c).to(layers.ground)
    }

    if (images.grass) {
      let c = { ...config.grass }
      let rb = ratio * c.blur
      if (rb < 2) {
        c.blur = c.blur - (rb / 2) * c.blur + 1
      } else {
        c.blur = rb - 2
      }

      c.sx = (c.sx || 0) - ratio * 25
      draw(images.grass, c).to(layers.grass)
    }

    if (images.threethree) {
      let c = { ...config.threethree }
      c.blur = c.blur - ratio * c.blur
      c.sx = (c.sx || 0) - ratio * 30
      draw(images.threethree, c).to(layers.threethree)
    }
  }

  let enterPoint = {}
  placeholder.value.addEventListener('mouseenter', (e) => {
    const { width } = placeholder.value.getBoundingClientRect()
    enterPoint.left = e.clientX
    enterPoint.right = width - e.clientX
  })
  placeholder.value.addEventListener('mousemove', (e) => {
    const v = e.clientX - enterPoint.left

    let ratio
    if (v < 0) {
      ratio = v / enterPoint.left
    } else {
      ratio = v / enterPoint.right
    }

    requestAnimationFrame(() => render(ratio))
  })
  placeholder.value.addEventListener('mouseout', (e) => {
    const v = e.clientX - enterPoint.left

    let ratio, gap
    if (v < 0) {
      ratio = v / enterPoint.left
      gap = 0.08
    } else {
      ratio = v / enterPoint.right
      gap = -0.08
    }

    requestAnimationFrame(tick)
    function tick() {
      if (gap * ratio < 0) {
        ratio = ratio + gap
        render(ratio)
        requestAnimationFrame(tick)
      } else {
        if (images.bg) {
          draw(images.bg, config.bg).to(layers.bg)
        }

        if (images.twotwo) {
          config.twotwo.blur = 1
          draw(images.twotwo, config.twotwo).to(layers.twotwo)
        }

        if (images.land) {
          draw(images.land, config.land).to(layers.land)
        }

        if (images.ground) {
          draw(images.ground, config.ground).to(layers.ground)
        }

        if (images.grass) {
          draw(images.grass, config.grass).to(layers.grass)
        }

        if (images.threethree) {
          draw(images.threethree, config.threethree).to(layers.threethree)
        }
      }
    }
  })

  watch(
    () => images.bg,
    () => draw(images.bg, config.bg).to(layers.bg)
  )

  watch(
    () => images.twotwo,
    () => draw(images.twotwo, config.twotwo).to(layers.twotwo)
  )

  watch(
    () => images.land,
    () => draw(images.land, config.land).to(layers.land)
  )

  watch(
    () => images.ground,
    () => draw(images.ground, config.ground).to(layers.ground)
  )

  watch(
    () => images.grass,
    () => draw(images.grass, config.grass).to(layers.grass)
  )

  watch(
    () => images.threethree,
    () => draw(images.threethree, config.threethree).to(layers.threethree)
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
  }

  &-layer {
    position: absolute;
    top: 0;
    left: 0;
  }
}
</style>
