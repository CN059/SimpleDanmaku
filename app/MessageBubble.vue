<!-- components/MessageBubble.vue -->
<template>
  <div
    v-if="isVisible"
    class="message-bubble fixed top-1/2 transform -translate-y-1/2 bg-gray-700 text-white px-6 py-3 rounded-full shadow-lg transition-transform duration-1000 ease-linear z-50"
    :style="{
      right: isVisible ? '-300px' : '100vw',
      transform: `translateY(-50%) translateX(${isVisible ? '0' : '-100vw'})`,
    }"
  >
    {{ message }}
  </div>
</template>

<script setup lang="ts">
const props = defineProps<{
  message: string;
  isVisible: boolean;
}>();
</script>

<style scoped>
/* 可选：增加一点入场缓动（但题目要求匀速，所以用 linear） */
.message-bubble {
  white-space: nowrap;
  max-width: 80vw;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>
