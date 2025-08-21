<script setup lang="ts">
import { Jimp } from 'jimp'

const props = withDefaults(defineProps<{
  imageUrl: string
  date?: string
  mode?: 'cover' | 'contain'
}>(), {
  mode: 'cover',
})

// 分析图片长宽比并决定是否需要旋转
function analyzeImage() {
  return new Promise((resolve) => {
    const img = new Image()
    img.onload = () => {
      const { width, height } = img

      // 如果图片是竖图（高度大于宽度），则旋转90度
      if (height > width) {
        resolve(true)
      }
      else {
        resolve(false)
      }
    }

    img.onerror = () => {
      console.error('图片加载失败:', props.imageUrl)
    }

    img.src = props.imageUrl
  })
}

const rotate = computedAsync(analyzeImage, false)

const renderImg = computedAsync(async () => {
  if (rotate.value) {
    const img = await Jimp.read(props.imageUrl)
    img.rotate(270)
    return await img.getBase64('image/jpeg')
  }
  else {
    return props.imageUrl
  }
})
</script>

<template>
  <div class="relative h-screen w-screen">
    <img
      :src="renderImg" :style="{
        height: '100%',
        width: '100%',
        objectFit: mode,
      }"
    >

    <template v-if="false">
      <span
        class="absolute text-sm text-white font-serif bottom-bleeding-4 left-bleeding-4" :class="{
          'write-vertical-left top-bleeding-4': rotate,
        }"
      >
        Photograph by Cinea
      </span>
      <span
        v-if="date" class="absolute text-sm text-white font-serif bottom-bleeding-4 right-bleeding-4" :class="{
          'write-vertical-left left-bleeding-4': rotate,
        }"
      >
        {{ date }}
      </span>
    </template>
  </div>
</template>
