<!-- app.vue -->
<template>
  <div class="min-h-screen bg-gray-50 p-8 relative">
    <div class="space-x-4 mb-8">
      <UButton @click="sendDanmaku" color="primary" size="lg">
        发送默认弹幕
      </UButton>
      <UButton @click="sendFastDanmaku" color="warning" size="lg">
        发送快速弹幕
      </UButton>
      <UButton @click="sendSlowDanmaku" color="success" size="lg">
        发送慢速弹幕
      </UButton>
    </div>
    <DanmakuLayer ref="danmaku" @launched="onLaunched" :default-speed="130" />
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import DanmakuLayer from "~/components/DanmakuLayer.vue";

const danmaku = ref<InstanceType<typeof DanmakuLayer> | null>(null);

function sendDanmaku() {
  danmaku.value?.launch("默认速度弹幕");
}

function sendFastDanmaku() {
  danmaku.value?.launch("快速弹幕", 200);
}

function sendSlowDanmaku() {
  danmaku.value?.launch("慢速弹幕", 60);
}

function onLaunched() {
  console.log("[弹幕已发射]");
}
</script>
