<script setup lang="ts">
import { onMounted, ref } from 'vue';
import gsap from 'gsap';
import Button from '@/components/ui/button.vue';
import { Heart, Sparkles, Music } from 'lucide-vue-next';

const titleRef = ref<HTMLElement | null>(null);
const subtitleRef = ref<HTMLElement | null>(null);
const ctaRef = ref<HTMLElement | null>(null);

onMounted(() => {
  const tl = gsap.timeline({ defaults: { ease: 'power3.out' } });

  tl.fromTo(
    titleRef.value,
    { opacity: 0, y: 30, scale: 0.95 },
    { opacity: 1, y: 0, scale: 1, duration: 1.2, delay: 0.2 }
  )
    .fromTo(
      subtitleRef.value,
      { opacity: 0, y: 20 },
      { opacity: 1, y: 0, duration: 1 },
      '-=0.8'
    )
    .fromTo(
      ctaRef.value,
      { opacity: 0, y: 20 },
      { opacity: 1, y: 0, duration: 0.8 },
      '-=0.6'
    );
});
</script>

<template>
  <div class="relative z-10 flex min-h-screen flex-col items-center justify-center px-4 text-center">
    <div class="inline-flex items-center gap-2 rounded-full border border-rose-200/60 bg-rose-50/70 px-4 py-1.5 text-xs sm:text-sm font-medium text-rose-600 backdrop-blur-md mb-6 shadow-xs">
      <Sparkles class="size-3.5 text-rose-500 animate-pulse" />
      <span>Specially crafted for you</span>
    </div>

    <h1
      ref="titleRef"
      class="text-4xl sm:text-6xl md:text-7xl font-extrabold tracking-tight text-slate-900 leading-tight mb-4"
    >
      To the One Who Holds <br class="hidden sm:inline" />
      <span class="bg-gradient-to-r from-rose-500 via-pink-500 to-purple-500 bg-clip-text text-transparent">
        My Whole Heart
      </span>
    </h1>

    <p
      ref="subtitleRef"
      class="max-w-lg text-base sm:text-lg text-slate-600 font-normal leading-relaxed mb-8 px-2"
    >
      Every single moment, smile, and heartbeat feels softer and warmer since you became a part of my world.
    </p>

    <div ref="ctaRef" class="flex flex-wrap items-center justify-center gap-4">
      <Button size="lg" class="rounded-full shadow-lg shadow-rose-500/25">
        <Heart class="size-4 fill-current mr-1 text-white animate-bounce" />
        Explore Our Journey
      </Button>
      <Button variant="outline" size="lg" class="rounded-full border-rose-300">
        <Music class="size-4 text-rose-500 mr-1" />
        Our Song
      </Button>
    </div>
  </div>
</template>
