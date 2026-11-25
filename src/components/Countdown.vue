<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';
import gsap from 'gsap';

// Target: 5 Desember pukul 00:00:00
function getTargetDate() {
  const now = new Date();
  let year = now.getFullYear();
  let target = new Date(year, 11, 5, 0, 0, 0);

  if (now.getTime() > target.getTime()) {
    target = new Date(year + 1, 11, 5, 0, 0, 0);
  }
  return target;
}

const days = ref(0);
const hours = ref(0);
const seconds = ref(0);
let timerId: number | null = null;

function updateCountdown() {
  const target = getTargetDate();
  const now = new Date();
  const diff = target.getTime() - now.getTime();

  if (diff <= 0) {
    days.value = 0;
    hours.value = 0;
    seconds.value = 0;
    return;
  }

  const totalSeconds = Math.floor(diff / 1000);
  const totalMinutes = Math.floor(totalSeconds / 60);
  const totalHours = Math.floor(totalMinutes / 60);

  days.value = Math.floor(totalHours / 24);
  hours.value = totalHours % 24;
  seconds.value = totalSeconds % 60;
}

// Canvas Burst Effect High Performance & Rich Visuals
const countdownWrapperRef = ref<HTMLElement | null>(null);
const burstCanvasRef = ref<HTMLCanvasElement | null>(null);

interface BurstParticleItem {
  type: 'heart' | 'sparkle' | 'ring';
  x: number;
  y: number;
  vx: number;
  vy: number;
  gravity: number;
  drag: number;
  size: number;
  color: string;
  rotation: number;
  rotSpeed: number;
  life: number;
  maxLife: number;
  wobbleSpeed: number;
  wobbleDistance: number;
}

const burstParticles: BurstParticleItem[] = [];
let burstAnimId: number | null = null;

const vibrantColors = [
  '#f43f5e', // rose-500
  '#fb7185', // rose-400
  '#fda4af', // rose-300
  '#e11d48', // rose-600
  '#ec4899', // pink-500
  '#f472b6', // pink-400
  '#fbcfe8', // pink-200
  '#ffedd5', // warm peach glow
  '#ffffff', // sparkling white
];

function drawHeartPath(ctx: CanvasRenderingContext2D, size: number) {
  ctx.beginPath();
  ctx.moveTo(0, 0);
  ctx.bezierCurveTo(-size * 0.7, -size * 0.7, -size * 1.2, size * 0.3, 0, size * 1.2);
  ctx.bezierCurveTo(size * 1.2, size * 0.3, size * 0.7, -size * 0.7, 0, 0);
  ctx.fill();
}

function drawSparkle(ctx: CanvasRenderingContext2D, size: number) {
  ctx.beginPath();
  for (let i = 0; i < 4; i++) {
    const angle = (i * Math.PI) / 2;
    const innerAngle = angle + Math.PI / 4;
    const rOuter = size;
    const rInner = size * 0.25;

    if (i === 0) {
      ctx.moveTo(Math.cos(angle) * rOuter, Math.sin(angle) * rOuter);
    } else {
      ctx.lineTo(Math.cos(angle) * rOuter, Math.sin(angle) * rOuter);
    }
    ctx.lineTo(Math.cos(innerAngle) * rInner, Math.sin(innerAngle) * rInner);
  }
  ctx.closePath();
  ctx.fill();
}

function renderBurstLoop() {
  const canvas = burstCanvasRef.value;
  if (!canvas) return;
  const ctx = canvas.getContext('2d');
  if (!ctx) return;

  ctx.clearRect(0, 0, canvas.width, canvas.height);

  for (let i = burstParticles.length - 1; i >= 0; i--) {
    const p = burstParticles[i];
    p.life++;

    p.vx *= p.drag;
    p.vy = p.vy * p.drag + p.gravity;
    p.x += p.vx + Math.sin(p.life * p.wobbleSpeed) * p.wobbleDistance;
    p.y += p.vy;
    p.rotation += p.rotSpeed;

    const progress = p.life / p.maxLife;
    if (progress >= 1) {
      burstParticles.splice(i, 1);
      continue;
    }

    let alpha = 1;
    if (progress > 0.6) {
      alpha = 1 - (progress - 0.6) / 0.4;
    }

    let currentScale = 1;
    if (progress < 0.15) {
      currentScale = (progress / 0.15) * 1.25;
    } else {
      currentScale = 1.25 - 0.35 * ((progress - 0.15) / 0.85);
    }

    ctx.save();
    ctx.translate(p.x, p.y);
    ctx.rotate(p.rotation);
    ctx.globalAlpha = Math.max(0, Math.min(alpha, 1));
    ctx.fillStyle = p.color;

    ctx.shadowColor = p.color;
    ctx.shadowBlur = 10;

    if (p.type === 'heart') {
      drawHeartPath(ctx, p.size * currentScale);
    } else if (p.type === 'sparkle') {
      drawSparkle(ctx, p.size * currentScale);
    } else if (p.type === 'ring') {
      ctx.beginPath();
      ctx.arc(0, 0, p.size * (1 + progress * 2.5), 0, Math.PI * 2);
      ctx.strokeStyle = p.color;
      ctx.lineWidth = Math.max(1, 2.5 * (1 - progress));
      ctx.stroke();
    }

    ctx.restore();
  }

  if (burstParticles.length > 0) {
    burstAnimId = requestAnimationFrame(renderBurstLoop);
  } else {
    burstAnimId = null;
  }
}

function handleClick(event: MouseEvent) {
  if (!countdownWrapperRef.value || !burstCanvasRef.value) return;

  // Text bounce animation
  gsap.killTweensOf(countdownWrapperRef.value);
  gsap.timeline()
    .to(countdownWrapperRef.value, {
      scale: 0.88,
      duration: 0.1,
      ease: 'power2.in',
    })
    .to(countdownWrapperRef.value, {
      scale: 1.06,
      duration: 0.24,
      ease: 'back.out(3.5)',
    })
    .to(countdownWrapperRef.value, {
      scale: 1,
      duration: 0.2,
      ease: 'power2.out',
    });

  const canvas = burstCanvasRef.value;
  const rect = canvas.getBoundingClientRect();
  const originX = event.clientX - rect.left;
  const originY = event.clientY - rect.top;

  // Shockwave glow rings
  for (let r = 0; r < 2; r++) {
    burstParticles.push({
      type: 'ring',
      x: originX,
      y: originY,
      vx: 0,
      vy: 0,
      gravity: 0,
      drag: 1,
      size: 14 + r * 10,
      color: r === 0 ? '#fb7185' : '#fda4af',
      rotation: 0,
      rotSpeed: 0,
      life: 0,
      maxLife: 28 + r * 6,
      wobbleSpeed: 0,
      wobbleDistance: 0,
    });
  }

  // Multi-directional burst: Hearts & Sparkles
  const totalCount = 36;
  for (let i = 0; i < totalCount; i++) {
    const isSparkle = Math.random() < 0.35;
    const angle = Math.random() * Math.PI * 2;
    const speed = 3 + Math.random() * 8.5;

    burstParticles.push({
      type: isSparkle ? 'sparkle' : 'heart',
      x: originX + (Math.random() - 0.5) * 16,
      y: originY + (Math.random() - 0.5) * 16,
      vx: Math.cos(angle) * speed,
      vy: Math.sin(angle) * speed - (isSparkle ? 1.5 : 3.5),
      gravity: isSparkle ? 0.08 : 0.15,
      drag: 0.94,
      size: isSparkle ? 4 + Math.random() * 5 : 7 + Math.random() * 9,
      color: vibrantColors[Math.floor(Math.random() * vibrantColors.length)],
      rotation: Math.random() * Math.PI * 2,
      rotSpeed: (Math.random() - 0.5) * 0.14,
      life: 0,
      maxLife: 45 + Math.random() * 40,
      wobbleSpeed: 0.08 + Math.random() * 0.1,
      wobbleDistance: (Math.random() - 0.5) * 0.8,
    });
  }

  if (!burstAnimId) {
    burstAnimId = requestAnimationFrame(renderBurstLoop);
  }
}

onMounted(() => {
  updateCountdown();
  timerId = window.setInterval(updateCountdown, 1000);

  const canvas = burstCanvasRef.value;
  if (canvas) {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
  }

  const handleResize = () => {
    if (!canvas) return;
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
  };
  window.addEventListener('resize', handleResize);

  onBeforeUnmount(() => {
    window.removeEventListener('resize', handleResize);
  });
});

onBeforeUnmount(() => {
  if (timerId !== null) clearInterval(timerId);
  if (burstAnimId !== null) cancelAnimationFrame(burstAnimId);
});
</script>

<template>
  <div class="relative z-10 flex min-h-screen flex-col items-center justify-center px-4 select-none">
    <!-- Fullscreen Burst Canvas Overlay -->
    <canvas
      ref="burstCanvasRef"
      class="pointer-events-none fixed inset-0 z-20 h-full w-full"
    ></canvas>

    <!-- Clickable Interactive Countdown Container -->
    <div
      ref="countdownWrapperRef"
      @click="handleClick"
      class="relative z-10 text-center cursor-pointer transition-transform group py-4 px-6 rounded-3xl"
    >
      <!-- Dedikasi di Atas Countdown -->
      <p
        class="font-romance text-3xl sm:text-4xl md:text-5xl tracking-wide text-rose-500 drop-shadow-sm mb-2 transition-colors duration-200 group-hover:text-rose-600"
      >
        For my lovely girl, Febri Santi Wulandari
      </p>

      <!-- Teks Countdown -->
      <h1
        class="font-romance text-4xl sm:text-6xl md:text-8xl tracking-wider text-rose-500 drop-shadow-sm transition-colors duration-200 group-hover:text-rose-600 active:scale-95"
      >
        {{ days }}-Hari, {{ hours }}-jam, {{ seconds }}-detik
      </h1>

      <!-- Kalimat di Bawah Countdown: Font & Warna Sama (Romance Delighter & text-rose-500), Ukuran Kecil -->
      <p
        class="font-romance mt-4 sm:mt-6 text-lg sm:text-xl md:text-2xl tracking-wide text-rose-500 drop-shadow-xs transition-colors duration-200 group-hover:text-rose-600"
      >
        Mohon ditunggu yaa sayangkuu
      </p>
    </div>
  </div>
</template>
