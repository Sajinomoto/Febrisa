<script setup lang="ts">
import { ref, onMounted } from 'vue';
import gsap from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';
import Card from '@/components/ui/card.vue';
import { Calendar, MapPin, Heart } from 'lucide-vue-next';

gsap.registerPlugin(ScrollTrigger);

interface TimelineEvent {
  date: string;
  title: string;
  location?: string;
  description: string;
  tag: string;
}

const events: TimelineEvent[] = [
  {
    date: 'Hari Pertama Bertemu',
    title: 'Awal Pertemuan yang Tak Terduga',
    location: 'Tempat Pertama Kali Bertemu',
    description: 'Di sinilah semua cerita indah ini dimulai. Senyum pertama yang membuat duniaku terasa berbeda.',
    tag: 'First Hello',
  },
  {
    date: 'Kencan Pertama',
    title: 'Secangkir Kopi & Percakapan Panjang',
    location: 'Sudut Kafe Favorit',
    description: 'Waktu terasa berjalan begitu cepat saat berbicara denganmu, sampai lupa bahwa senja sudah berganti malam.',
    tag: 'First Date',
  },
  {
    date: 'Hari Resmi Bersama',
    title: 'Dua Hati Memilih Satu Tujuan',
    location: 'Di Bawah Langit Malam',
    description: 'Hari saat kita memutuskan untuk saling melengkapi, saling menjaga, dan berjalan beriringan.',
    tag: 'Our Beginning',
  },
  {
    date: 'Hari Ini & Seterusnya',
    title: 'Setiap Hari Selalu Jatuh Cinta Lagi',
    location: 'Di Mana Pun Bersamamu',
    description: 'Terima kasih telah hadir dan mewarnai setiap hari dalam hidupku dengan kehangatan dan cintamu.',
    tag: 'Forever & Always',
  },
];

const sectionRef = ref<HTMLElement | null>(null);

onMounted(() => {
  if (!sectionRef.value) return;

  const items = sectionRef.value.querySelectorAll('.timeline-item');
  items.forEach((item, index) => {
    gsap.fromTo(
      item,
      {
        opacity: 0,
        y: 40,
        scale: 0.96,
      },
      {
        opacity: 1,
        y: 0,
        scale: 1,
        duration: 0.8,
        delay: index * 0.1,
        scrollTrigger: {
          trigger: item,
          start: 'top 85%',
          toggleActions: 'play none none reverse',
        },
      }
    );
  });
});
</script>

<template>
  <section ref="sectionRef" class="relative z-10 py-24 px-4 sm:px-6 lg:px-8 max-w-4xl mx-auto">
    <!-- Section Header -->
    <div class="text-center mb-16">
      <div class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-rose-100/70 text-rose-600 text-xs font-semibold mb-3">
        <Heart class="size-3.5 fill-current" />
        <span>Jejak Cerita Kita</span>
      </div>
      <h2 class="text-3xl sm:text-4xl font-bold tracking-tight text-slate-900">
        Timeline Kenangan Indah
      </h2>
      <p class="mt-3 text-slate-600 text-sm sm:text-base max-w-md mx-auto">
        Setiap babak perjalanan yang membawa kita sampai di hari yang istimewa ini.
      </p>
    </div>

    <!-- Timeline List -->
    <div class="relative border-l-2 border-rose-200/80 ml-4 sm:ml-32 space-y-12">
      <div
        v-for="(event, idx) in events"
        :key="idx"
        class="timeline-item relative pl-8 group"
      >
        <!-- Timeline node dot -->
        <div class="absolute -left-[17px] top-1.5 size-8 rounded-full border-2 border-rose-300 bg-white flex items-center justify-center shadow-xs transition-transform duration-300 group-hover:scale-110 group-hover:border-rose-500">
          <Heart class="size-3.5 text-rose-500 fill-rose-500/30" />
        </div>

        <!-- Date label for desktop -->
        <div class="hidden sm:block absolute -left-36 top-2 text-right w-28 text-xs font-semibold text-rose-600">
          {{ event.date }}
        </div>

        <!-- Card Content -->
        <Card class="p-6 transition-all hover:-translate-y-1">
          <!-- Mobile Date -->
          <div class="sm:hidden flex items-center gap-1.5 text-xs font-semibold text-rose-600 mb-2">
            <Calendar class="size-3.5" />
            <span>{{ event.date }}</span>
          </div>

          <div class="flex items-start justify-between gap-4 mb-2">
            <h3 class="text-lg sm:text-xl font-bold text-slate-900">
              {{ event.title }}
            </h3>
            <span class="shrink-0 text-xs px-2.5 py-0.5 rounded-full font-medium bg-rose-50 text-rose-600 border border-rose-200/50">
              {{ event.tag }}
            </span>
          </div>

          <div v-if="event.location" class="flex items-center gap-1.5 text-xs text-slate-500 mb-3">
            <MapPin class="size-3 text-slate-400" />
            <span>{{ event.location }}</span>
          </div>

          <p class="text-sm text-slate-600 leading-relaxed">
            {{ event.description }}
          </p>
        </Card>
      </div>
    </div>
  </section>
</template>
