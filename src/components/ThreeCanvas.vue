<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';

const canvasRef = ref<HTMLCanvasElement | null>(null);
let animationFrameId: number | null = null;

interface HeartParticle {
  x: number;
  y: number;
  size: number;
  color: string;
  rotation: number;
  rotSpeed: number;
  vx: number;
  vy: number;
  life: number;        // progress 0 -> 1
  duration: number;    // detik (2 - 5s)
}

onMounted(() => {
  const canvas = canvasRef.value;
  if (!canvas) return;

  const ctx = canvas.getContext('2d');
  if (!ctx) return;

  let width = (canvas.width = window.innerWidth);
  let height = (canvas.height = window.innerHeight);

  // Palet warna variatif pastel & romantis
  const colorPalette = [
    'rgba(244, 63, 94, ',   // rose-500
    'rgba(236, 72, 153, ',  // pink-500
    'rgba(251, 113, 133, ', // rose-400
    'rgba(244, 114, 182, ', // pink-400
    'rgba(168, 85, 247, ',  // purple-500
    'rgba(192, 132, 252, ', // purple-400
    'rgba(253, 164, 175, ', // rose-300
    'rgba(249, 168, 212, ', // pink-300
  ];

  function drawHeart(
    context: CanvasRenderingContext2D,
    x: number,
    y: number,
    size: number,
    rotation: number,
    colorBase: string,
    opacity: number
  ) {
    context.save();
    context.translate(x, y);
    context.rotate(rotation);
    context.scale(size, size);

    context.beginPath();
    context.moveTo(0, 0);
    context.bezierCurveTo(-15, -15, -25, 5, 0, 25);
    context.bezierCurveTo(25, 5, 15, -15, 0, 0);

    context.fillStyle = colorBase + opacity + ')';
    context.fill();
    context.restore();
  }

  // Populasi love dikurangi agar tidak terlalu ramai (20 untuk mobile, 38 untuk desktop)
  const targetCount = window.innerWidth < 768 ? 20 : 38;
  const particles: HeartParticle[] = [];

  function createHeart(spawnRandomLife = false): HeartParticle {
    const duration = 2 + Math.random() * 3; // 2 sampai 5 detik
    return {
      x: Math.random() * width,
      y: Math.random() * height,
      size: 0.35 + Math.random() * 0.65,
      color: colorPalette[Math.floor(Math.random() * colorPalette.length)],
      rotation: (Math.random() - 0.5) * 0.5,
      rotSpeed: (Math.random() - 0.5) * 0.012,
      vx: (Math.random() - 0.5) * 0.35,
      vy: -0.25 - Math.random() * 0.45,
      life: spawnRandomLife ? Math.random() : 0,
      duration: duration,
    };
  }

  for (let i = 0; i < targetCount; i++) {
    particles.push(createHeart(true));
  }

  let lastTime = performance.now();

  const render = (time: number) => {
    animationFrameId = requestAnimationFrame(render);
    const dt = (time - lastTime) / 1000;
    lastTime = time;

    ctx.clearRect(0, 0, width, height);

    for (let i = 0; i < particles.length; i++) {
      const p = particles[i];

      p.life += dt / p.duration;

      if (p.life >= 1) {
        particles[i] = createHeart(false);
        continue;
      }

      p.x += p.vx;
      p.y += p.vy;
      p.rotation += p.rotSpeed;

      let alpha = 1;
      if (p.life < 0.2) {
        alpha = p.life / 0.2;
      } else if (p.life > 0.8) {
        alpha = (1 - p.life) / 0.2;
      }

      const scaleEffect = p.size * (0.85 + 0.15 * Math.sin(p.life * Math.PI));

      drawHeart(ctx, p.x, p.y, scaleEffect, p.rotation, p.color, Math.max(0, Math.min(alpha * 0.7, 1)));
    }
  };

  animationFrameId = requestAnimationFrame((t) => {
    lastTime = t;
    render(t);
  });

  const onResize = () => {
    if (!canvas) return;
    width = canvas.width = window.innerWidth;
    height = canvas.height = window.innerHeight;
  };

  window.addEventListener('resize', onResize);

  onBeforeUnmount(() => {
    if (animationFrameId !== null) cancelAnimationFrame(animationFrameId);
    window.removeEventListener('resize', onResize);
  });
});
</script>

<template>
  <canvas ref="canvasRef" class="pointer-events-none fixed inset-0 z-0 h-full w-full"></canvas>
</template>
