<!-- app.vue -->
<template>
  <div class="min-h-screen bg-gray-50 p-8 relative overflow-hidden">
    <UButton @click="launchBubble" color="primary"> 发送弹幕 </UButton>

    <div
      v-for="bubble in activeBubbles"
      :key="bubble.id"
      ref="bubbleRefs"
      class="bubble fixed top-5 bg-gray-700 text-white py-2 px-4 rounded-full pointer-events-none select-none whitespace-nowrap z-50"
      :style="{
        left: bubble.x + 'px',
      }"
    >
      {{ bubble.message }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";

interface Bubble {
  id: number;
  x: number;
  speed: number;
  message: string;
}

const activeBubbles = ref<Bubble[]>([]);
const bubbleRefs = ref<HTMLElement[]>([]);
const rafId = ref<number | null>(null);
let globalId = 0;
let lastTime = 0;

onMounted(() => {
  startPhysicsLoop();
});

onUnmounted(() => {
  if (rafId.value) cancelAnimationFrame(rafId.value);
});

function launchBubble() {
  const newBubble: Bubble = {
    id: globalId++,
    x: window.innerWidth + 100,
    speed: 130,
    message: "这是一条弹幕",
  };
  activeBubbles.value.push(newBubble);
}

function startPhysicsLoop() {
  const loop = () => {
    const now = performance.now();

    if (!lastTime) lastTime = now; // 初始化

    const delta = (now - lastTime) / 1000;
    lastTime = now; // 更新

    // 更新每个气泡位置 + 检测是否出屏
    activeBubbles.value = activeBubbles.value.filter((bubble) => {
      bubble.x -= bubble.speed * delta;

      const el = bubbleRefs.value.find(
        (el) => el?.textContent === bubble.message
      );
      if (!el) return true;

      const rect = el.getBoundingClientRect();
      return rect.right > 0; // 未完全离开屏幕左侧 → 保留
    });

    rafId.value = requestAnimationFrame(loop);
  };

  rafId.value = requestAnimationFrame(loop);
}
</script>
