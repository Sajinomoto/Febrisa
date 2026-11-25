<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from 'vue';
import * as THREE from 'three';

const containerRef = ref<HTMLDivElement | null>(null);

let renderer: THREE.WebGLRenderer | null = null;
let scene: THREE.Scene | null = null;
let camera: THREE.PerspectiveCamera | null = null;
let animationFrameId: number | null = null;

onMounted(() => {
  if (!containerRef.value) return;

  const container = containerRef.value;
  const width = container.clientWidth;
  const height = container.clientHeight;

  // Scene
  scene = new THREE.Scene();

  // Camera
  camera = new THREE.PerspectiveCamera(60, width / height, 0.1, 1000);
  camera.position.z = 25;

  // Renderer with low-power & high performance setup for mobile
  renderer = new THREE.WebGLRenderer({
    alpha: true,
    antialias: window.devicePixelRatio < 2, // disable heavy antialiasing on retina/high-dpi phones
    powerPreference: 'high-performance',
  });
  renderer.setSize(width, height);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  container.appendChild(renderer.domElement);

  // Particles (floating glowing orbs/hearts)
  const particleCount = window.innerWidth < 768 ? 120 : 300;
  const geometry = new THREE.BufferGeometry();
  const positions = new Float32Array(particleCount * 3);
  const colors = new Float32Array(particleCount * 3);

  const colorOptions = [
    new THREE.Color('#f43f5e'), // rose
    new THREE.Color('#ec4899'), // pink
    new THREE.Color('#fb7185'), // light rose
    new THREE.Color('#fda4af'), // pale rose
  ];

  for (let i = 0; i < particleCount; i++) {
    positions[i * 3] = (Math.random() - 0.5) * 50;
    positions[i * 3 + 1] = (Math.random() - 0.5) * 50;
    positions[i * 3 + 2] = (Math.random() - 0.5) * 30;

    const chosenColor = colorOptions[Math.floor(Math.random() * colorOptions.length)];
    colors[i * 3] = chosenColor.r;
    colors[i * 3 + 1] = chosenColor.g;
    colors[i * 3 + 2] = chosenColor.b;
  }

  geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
  geometry.setAttribute('color', new THREE.BufferAttribute(colors, 3));

  // Point Material
  const material = new THREE.PointsMaterial({
    size: 0.6,
    vertexColors: true,
    transparent: true,
    opacity: 0.8,
    blending: THREE.AdditiveBlending,
  });

  const particleSystem = new THREE.Points(geometry, material);
  scene.add(particleSystem);

  // Responsive resize
  const onResize = () => {
    if (!container || !renderer || !camera) return;
    const w = container.clientWidth;
    const h = container.clientHeight;
    camera.aspect = w / h;
    camera.updateProjectionMatrix();
    renderer.setSize(w, h);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  };

  window.addEventListener('resize', onResize);

  // Animation Loop with low overhead
  let clock = new THREE.Clock();
  const animate = () => {
    animationFrameId = requestAnimationFrame(animate);
    const elapsedTime = clock.getElapsedTime();

    particleSystem.rotation.y = elapsedTime * 0.05;
    particleSystem.rotation.x = Math.sin(elapsedTime * 0.03) * 0.1;

    renderer?.render(scene!, camera!);
  };

  animate();

  onBeforeUnmount(() => {
    if (animationFrameId !== null) cancelAnimationFrame(animationFrameId);
    window.removeEventListener('resize', onResize);
    geometry.dispose();
    material.dispose();
    renderer?.dispose();
    if (container && renderer?.domElement) {
      container.removeChild(renderer.domElement);
    }
  });
});
</script>

<template>
  <div ref="containerRef" class="pointer-events-none absolute inset-0 z-0 overflow-hidden"></div>
</template>
