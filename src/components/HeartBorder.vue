<script setup lang="ts">
// Pola 4 tile sesuai referensi user
interface TilePattern {
  bgColor: string;
  heartFill: string;
  heartStroke: string;
}

const pattern: TilePattern[] = [
  // 1. Kotak 1: Pink lembut + Hati merah + Outline putih
  { bgColor: '#ff6b95', heartFill: '#ff3b30', heartStroke: '#ffffff' },
  // 2. Kotak 2: Putih + Hati pink cerah + Outline merah
  { bgColor: '#ffffff', heartFill: '#ff66cc', heartStroke: '#ff2d55' },
  // 3. Kotak 3: Magenta pink cerah + Hati soft red/pink + Outline putih
  { bgColor: '#ff69d4', heartFill: '#ff4d79', heartStroke: '#ffffff' },
  // 4. Kotak 4: Merah menyala + Hati putih + Outline pink fuchsia
  { bgColor: '#ff2b2b', heartFill: '#ffffff', heartStroke: '#ff4db8' },
];

// Buat 1 set tiles yang sangat panjang (15 set = 60 tiles)
const setRepeat = 15;
const tileSet = Array.from({ length: setRepeat }, () => pattern).flat();
</script>

<template>
  <div class="pointer-events-none fixed inset-0 z-30 select-none overflow-hidden">
    <!-- 1. BORDER ATAS (TOP): Geser ke KIRI -->
    <div class="absolute top-0 left-0 right-0 h-8 sm:h-10 overflow-hidden shadow-xs">
      <div class="marquee-h-to-left flex h-full">
        <!-- Set A -->
        <div class="flex h-full shrink-0">
          <div
            v-for="(tile, idx) in tileSet"
            :key="`top-a-${idx}`"
            class="aspect-square h-full flex items-center justify-center p-1.5 shrink-0"
            :style="{ backgroundColor: tile.bgColor }"
          >
            <svg viewBox="0 0 24 24" class="h-full w-full">
              <path
                d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                :fill="tile.heartFill"
                :stroke="tile.heartStroke"
                stroke-width="2.2"
                stroke-linejoin="round"
              />
            </svg>
          </div>
        </div>
        <!-- Set B -->
        <div class="flex h-full shrink-0">
          <div
            v-for="(tile, idx) in tileSet"
            :key="`top-b-${idx}`"
            class="aspect-square h-full flex items-center justify-center p-1.5 shrink-0"
            :style="{ backgroundColor: tile.bgColor }"
          >
            <svg viewBox="0 0 24 24" class="h-full w-full">
              <path
                d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                :fill="tile.heartFill"
                :stroke="tile.heartStroke"
                stroke-width="2.2"
                stroke-linejoin="round"
              />
            </svg>
          </div>
        </div>
      </div>
    </div>

    <!-- 2. BORDER BAWAH (BOTTOM): Geser ke KANAN -->
    <div class="absolute bottom-0 left-0 right-0 h-8 sm:h-10 overflow-hidden shadow-xs">
      <div class="marquee-h-to-right flex h-full">
        <!-- Set A -->
        <div class="flex h-full shrink-0">
          <div
            v-for="(tile, idx) in tileSet"
            :key="`bottom-a-${idx}`"
            class="aspect-square h-full flex items-center justify-center p-1.5 shrink-0"
            :style="{ backgroundColor: tile.bgColor }"
          >
            <svg viewBox="0 0 24 24" class="h-full w-full">
              <path
                d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                :fill="tile.heartFill"
                :stroke="tile.heartStroke"
                stroke-width="2.2"
                stroke-linejoin="round"
              />
            </svg>
          </div>
        </div>
        <!-- Set B -->
        <div class="flex h-full shrink-0">
          <div
            v-for="(tile, idx) in tileSet"
            :key="`bottom-b-${idx}`"
            class="aspect-square h-full flex items-center justify-center p-1.5 shrink-0"
            :style="{ backgroundColor: tile.bgColor }"
          >
            <svg viewBox="0 0 24 24" class="h-full w-full">
              <path
                d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                :fill="tile.heartFill"
                :stroke="tile.heartStroke"
                stroke-width="2.2"
                stroke-linejoin="round"
              />
            </svg>
          </div>
        </div>
      </div>
    </div>

    <!-- 3. BORDER KANAN (RIGHT): Geser ke ATAS (Diperlambat menjadi 80 detik agar jauh lebih lembut) -->
    <div class="absolute top-0 bottom-0 right-0 w-8 sm:w-10 overflow-hidden shadow-xs">
      <div class="marquee-v-to-up flex flex-col w-full">
        <!-- Set A -->
        <div class="flex flex-col w-full shrink-0">
          <div
            v-for="(tile, idx) in tileSet"
            :key="`right-a-${idx}`"
            class="aspect-square w-full flex items-center justify-center p-1.5 shrink-0"
            :style="{ backgroundColor: tile.bgColor }"
          >
            <svg viewBox="0 0 24 24" class="h-full w-full">
              <path
                d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                :fill="tile.heartFill"
                :stroke="tile.heartStroke"
                stroke-width="2.2"
                stroke-linejoin="round"
              />
            </svg>
          </div>
        </div>
        <!-- Set B -->
        <div class="flex flex-col w-full shrink-0">
          <div
            v-for="(tile, idx) in tileSet"
            :key="`right-b-${idx}`"
            class="aspect-square w-full flex items-center justify-center p-1.5 shrink-0"
            :style="{ backgroundColor: tile.bgColor }"
          >
            <svg viewBox="0 0 24 24" class="h-full w-full">
              <path
                d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                :fill="tile.heartFill"
                :stroke="tile.heartStroke"
                stroke-width="2.2"
                stroke-linejoin="round"
              />
            </svg>
          </div>
        </div>
      </div>
    </div>

    <!-- 4. BORDER KIRI (LEFT): Geser ke BAWAH (Diperlambat menjadi 80 detik agar jauh lebih lembut) -->
    <div class="absolute top-0 bottom-0 left-0 w-8 sm:w-10 overflow-hidden shadow-xs">
      <div class="marquee-v-to-down flex flex-col w-full">
        <!-- Set A -->
        <div class="flex flex-col w-full shrink-0">
          <div
            v-for="(tile, idx) in tileSet"
            :key="`left-a-${idx}`"
            class="aspect-square w-full flex items-center justify-center p-1.5 shrink-0"
            :style="{ backgroundColor: tile.bgColor }"
          >
            <svg viewBox="0 0 24 24" class="h-full w-full">
              <path
                d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                :fill="tile.heartFill"
                :stroke="tile.heartStroke"
                stroke-width="2.2"
                stroke-linejoin="round"
              />
            </svg>
          </div>
        </div>
        <!-- Set B -->
        <div class="flex flex-col w-full shrink-0">
          <div
            v-for="(tile, idx) in tileSet"
            :key="`left-b-${idx}`"
            class="aspect-square w-full flex items-center justify-center p-1.5 shrink-0"
            :style="{ backgroundColor: tile.bgColor }"
          >
            <svg viewBox="0 0 24 24" class="h-full w-full">
              <path
                d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 4.42 3 7.5 3c1.74 0 3.41.81 4.5 2.09C13.09 3.81 14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 6.86-8.55 11.54L12 21.35z"
                :fill="tile.heartFill"
                :stroke="tile.heartStroke"
                stroke-width="2.2"
                stroke-linejoin="round"
              />
            </svg>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Border horizontal (Atas & Bawah) */
.marquee-h-to-left {
  animation: slide-left 45s linear infinite;
  will-change: transform;
}

.marquee-h-to-right {
  animation: slide-right 45s linear infinite;
  will-change: transform;
}

/* Border vertikal (Kanan & Kiri) dibuat 80s agar lajunya sangat lembut & seimbang dengan horizontal */
.marquee-v-to-up {
  animation: slide-up 80s linear infinite;
  will-change: transform;
}

.marquee-v-to-down {
  animation: slide-down 80s linear infinite;
  will-change: transform;
}

@keyframes slide-left {
  0% {
    transform: translate3d(0, 0, 0);
  }
  100% {
    transform: translate3d(-50%, 0, 0);
  }
}

@keyframes slide-right {
  0% {
    transform: translate3d(-50%, 0, 0);
  }
  100% {
    transform: translate3d(0, 0, 0);
  }
}

@keyframes slide-up {
  0% {
    transform: translate3d(0, 0, 0);
  }
  100% {
    transform: translate3d(0, -50%, 0);
  }
}

@keyframes slide-down {
  0% {
    transform: translate3d(0, -50%, 0);
  }
  100% {
    transform: translate3d(0, 0, 0);
  }
}
</style>
