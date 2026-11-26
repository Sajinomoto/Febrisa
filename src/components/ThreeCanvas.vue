<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';

const canvasRef = ref<HTMLCanvasElement | null>(null);
let animationFrameId: number | null = null;

// Bintang di langit atas
interface Star {
  x: number;
  y: number;
  radius: number;
  baseAlpha: number;
  twinkleSpeed: number;
  phase: number;
  isCrossSparkle: boolean;
}

// Partikel Hati 2D
interface HeartParticle {
  x: number;
  y: number;
  size: number;
  color: string;
  rotation: number;
  rotSpeed: number;
  vx: number;
  vy: number;
  life: number;
  duration: number;
}

onMounted(() => {
  const canvas = canvasRef.value;
  if (!canvas) return;

  const ctx = canvas.getContext('2d');
  if (!ctx) return;

  let width = (canvas.width = window.innerWidth);
  let height = (canvas.height = window.innerHeight);

  // Palet warna partikel hati bernuansa glowing
  const colorPalette = [
    'rgba(244, 63, 94, ',   // rose-500
    'rgba(251, 113, 133, ', // rose-400
    'rgba(244, 114, 182, ', // pink-400
    'rgba(236, 72, 153, ',  // pink-500
    'rgba(217, 70, 239, ',  // fuchsia-500
    'rgba(192, 132, 252, ', // purple-400
    'rgba(253, 164, 175, ', // rose-300
  ];

  // Helper gambar hati 2D
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
    context.shadowColor = colorBase + (opacity * 0.8) + ')';
    context.shadowBlur = 10;
    context.fill();
    context.restore();
  }

  // Helper gambar bintang 4 sudut
  function drawCrossStar(context: CanvasRenderingContext2D, cx: number, cy: number, size: number, alpha: number) {
    context.save();
    context.translate(cx, cy);
    context.fillStyle = `rgba(255, 250, 230, ${alpha})`;
    context.shadowColor = `rgba(255, 240, 190, ${alpha})`;
    context.shadowBlur = 8;

    context.beginPath();
    for (let i = 0; i < 4; i++) {
      const angle = (i * Math.PI) / 2;
      const innerAngle = angle + Math.PI / 4;
      const rOuter = size;
      const rInner = size * 0.22;

      if (i === 0) {
        context.moveTo(Math.cos(angle) * rOuter, Math.sin(angle) * rOuter);
      } else {
        context.lineTo(Math.cos(angle) * rOuter, Math.sin(angle) * rOuter);
      }
      context.lineTo(Math.cos(innerAngle) * rInner, Math.sin(innerAngle) * rInner);
    }
    context.closePath();
    context.fill();
    context.restore();
  }

  // 1. Inisialisasi Bintang
  const starCount = window.innerWidth < 768 ? 55 : 110;
  const stars: Star[] = [];

  for (let i = 0; i < starCount; i++) {
    stars.push({
      x: Math.random() * width,
      y: Math.random() * (height * 0.65),
      radius: 0.6 + Math.random() * 1.5,
      baseAlpha: 0.25 + Math.random() * 0.65,
      twinkleSpeed: 1 + Math.random() * 2.5,
      phase: Math.random() * Math.PI * 2,
      isCrossSparkle: Math.random() < 0.16,
    });
  }

  // 2. Inisialisasi Partikel Hati
  const heartCount = window.innerWidth < 768 ? 18 : 32;
  const hearts: HeartParticle[] = [];

  function createHeart(spawnRandomLife = false): HeartParticle {
    // Durasi hidup diperpanjang (4 sampai 8 detik) agar animasi melayang santai & lambat
    const duration = 4.0 + Math.random() * 4.0;
    return {
      x: Math.random() * width,
      y: Math.random() * height,
      size: 0.35 + Math.random() * 0.6,
      color: colorPalette[Math.floor(Math.random() * colorPalette.length)],
      rotation: (Math.random() - 0.5) * 0.5,
      rotSpeed: (Math.random() - 0.5) * 0.005,
      vx: (Math.random() - 0.5) * 0.12,
      vy: -0.08 - Math.random() * 0.16,
      life: spawnRandomLife ? Math.random() : 0,
      duration: duration,
    };
  }

  for (let i = 0; i < heartCount; i++) {
    hearts.push(createHeart(true));
  }

  let lastTime = performance.now();

  const render = (time: number) => {
    animationFrameId = requestAnimationFrame(render);
    const dt = (time - lastTime) / 1000;
    lastTime = time;

    ctx.clearRect(0, 0, width, height);

    // --- A. Gambar Bulan Bulat Sempurna (Full Moon) dengan Subtle Warm Glow ---
    const isMobile = width < 768;
    const moonCenterX = width * (isMobile ? 0.76 : 0.82);
    const moonCenterY = height * (isMobile ? 0.16 : 0.18);
    const moonRadius = isMobile ? 32 : 44;

    // 1. Halo Glow Lembut di Sekitar Bulan (Tidak Terlalu Kuat/Silau)
    const haloGlow = ctx.createRadialGradient(
      moonCenterX,
      moonCenterY,
      moonRadius * 0.8,
      moonCenterX,
      moonCenterY,
      moonRadius * 2.2
    );
    haloGlow.addColorStop(0, 'rgba(255, 240, 160, 0.28)');
    haloGlow.addColorStop(0.5, 'rgba(255, 230, 140, 0.10)');
    haloGlow.addColorStop(1, 'rgba(255, 220, 120, 0)');
    ctx.fillStyle = haloGlow;
    ctx.beginPath();
    ctx.arc(moonCenterX, moonCenterY, moonRadius * 2.2, 0, Math.PI * 2);
    ctx.fill();

    // 2. Bulan Bulat Sempurna (Kuning Cerah Alami)
    ctx.save();
    // Subtle edge glow tipis
    ctx.shadowColor = 'rgba(255, 240, 140, 0.75)';
    ctx.shadowBlur = 14;

    // Gradasi lembut pada permukaan bulan bulat
    const moonGrad = ctx.createRadialGradient(
      moonCenterX - moonRadius * 0.25,
      moonCenterY - moonRadius * 0.25,
      moonRadius * 0.1,
      moonCenterX,
      moonCenterY,
      moonRadius
    );
    moonGrad.addColorStop(0, '#fffbe6'); // highlight terang lembut
    moonGrad.addColorStop(0.5, '#ffea75'); // kuning cerah
    moonGrad.addColorStop(1, '#ffd043'); // tepi kuning keemasan hangat

    ctx.fillStyle = moonGrad;
    ctx.beginPath();
    ctx.arc(moonCenterX, moonCenterY, moonRadius, 0, Math.PI * 2);
    ctx.fill();
    ctx.restore();

    // --- B. Gambar Bintang-Bintang Berkelap-kelip ---
    const currentTimeSec = time / 1000;
    for (let i = 0; i < stars.length; i++) {
      const s = stars[i];
      const twinkle = 0.5 + 0.5 * Math.sin(currentTimeSec * s.twinkleSpeed + s.phase);
      const currentAlpha = s.baseAlpha * (0.4 + 0.6 * twinkle);

      if (s.isCrossSparkle && twinkle > 0.6) {
        drawCrossStar(ctx, s.x, s.y, s.radius * 2.8, currentAlpha);
      } else {
        ctx.beginPath();
        ctx.arc(s.x, s.y, s.radius, 0, Math.PI * 2);
        ctx.fillStyle = `rgba(255, 255, 255, ${currentAlpha})`;
        ctx.shadowColor = `rgba(255, 255, 255, ${currentAlpha * 0.8})`;
        ctx.shadowBlur = 4;
        ctx.fill();
      }
    }

    // --- C. Gambar Partikel Hati Melayang ---
    for (let i = 0; i < hearts.length; i++) {
      const p = hearts[i];
      p.life += dt / p.duration;

      if (p.life >= 1) {
        hearts[i] = createHeart(false);
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
      drawHeart(ctx, p.x, p.y, scaleEffect, p.rotation, p.color, Math.max(0, Math.min(alpha * 0.75, 1)));
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
