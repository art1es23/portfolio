<template>
  <div ref="scene" class="m-auto scene">CanvasScene</div>
</template>

<script>
export default {
  name: 'MainScene',
}
</script>

<script setup>
import { onMounted, ref } from 'vue'
import * as PIXI from 'pixi.js'

const scene = ref(null)
const app = ref(null)

onMounted(() => {
  app.value = new PIXI.Application({
    width: 800,
    height: 600,
    backgroundColor: 0x1099bb,
  })
})
</script>
