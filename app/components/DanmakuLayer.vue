<!-- components/DanmakuLayer.vue -->
<template>
  <div class="danmaku-container fixed inset-0 pointer-events-none z-9999">
    <div
      v-for="(bubble, index) in activeBubbles"
      :key="bubble.id"
      :ref="(el) => setRef(index, el)"
      class="bubble fixed bg-gray-700 text-white py-2 px-4 rounded-full pointer-events-none select-none whitespace-nowrap"
      :style="{
        left: bubble.x + 'px',
        top: bubble.y + '%',
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
  y: number;
  speed: number;
  message: string;
}

const MAX_BUBBLES = 1000;

const props = defineProps<{
  defaultSpeed?: number;
}>();

const emit = defineEmits<{
  (e: "launched"): void;
}>();

const activeBubbles = ref<Bubble[]>([]);
const bubbleElements = ref<(HTMLElement | null)[]>([]);
const rafId = ref<number | null>(null);
let globalId = 0;
let lastTime = 0;

onMounted(() => {
  startPhysicsLoop();
});

onUnmounted(() => {
  if (rafId.value) cancelAnimationFrame(rafId.value);
});

function getRandomY() {
  return 10 + Math.random() * 50;
}

function launch(
  message: string = "这是一条弹幕",
  speed: number = props.defaultSpeed || 130
) {
  if (activeBubbles.value.length >= MAX_BUBBLES) {
    activeBubbles.value.shift();
    bubbleElements.value.shift();
  }

  const newBubble: Bubble = {
    id: globalId++,
    x: window.innerWidth + 100,
    y: getRandomY(),
    speed,
    message,
  };

  activeBubbles.value.push(newBubble);
  bubbleElements.value.push(null);

  emit("launched");
}

defineExpose({
  launch,
  clearAll() {
    activeBubbles.value = [];
    bubbleElements.value = [];
  },
});

function setRef(index: number, el: Element | ComponentPublicInstance | null) {
  if (el && !(el instanceof Element)) return;
  bubbleElements.value[index] = el as HTMLElement | null;
}

function startPhysicsLoop() {
  const loop = () => {
    const now = performance.now();

    if (!lastTime) lastTime = now;

    const delta = (now - lastTime) / 1000;
    lastTime = now;
    for (let i = activeBubbles.value.length - 1; i >= 0; i--) {
      const bubble = activeBubbles.value[i]!;
      bubble.x -= bubble.speed * delta;

      const el = bubbleElements.value[i];
      if (!el) continue;

      const rect = el.getBoundingClientRect();
      if (rect.right < 0) {
        activeBubbles.value.splice(i, 1);
        bubbleElements.value.splice(i, 1);
      }
    }

    rafId.value = requestAnimationFrame(loop);
  };

  rafId.value = requestAnimationFrame(loop);
}
</script>

<style scoped>
.danmaku-container {
  pointer-events: none;
}

.bubble {
  pointer-events: none;
  z-index: 9999;
  font-size: 0.875rem;
  opacity: 0.95;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.4);
  will-change: transform;
}
</style>
