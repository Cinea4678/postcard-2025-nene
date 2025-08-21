<script setup lang="ts">
defineOptions({
  name: 'IndexPage',
})

const postcardSet = computedAsync(() => fetch('/postcard-set.json').then(res => res.json()))
const postcardList = computed(() => postcardSet.value?.postcards ?? [])
</script>

<template>
  <div class="flex flex-col">
    <PostCard
      v-for="p in postcardList" :key="p.imageUrl" v-bind="{
        imageUrl: p.imageUrl,
        date: p.date,
        mode: p.mode,
      }"
    />
  </div>
</template>
