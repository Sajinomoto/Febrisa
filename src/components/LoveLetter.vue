<script setup lang="ts">
import { ref } from 'vue';
import Card from '@/components/ui/card.vue';
import Button from '@/components/ui/button.vue';
import { Mail, Heart, Sparkles, Send } from 'lucide-vue-next';

const isOpen = ref(false);

const toggleLetter = () => {
  isOpen.value = !isOpen.value;
};
</script>

<template>
  <section class="relative z-10 py-20 px-4 max-w-2xl mx-auto text-center">
    <div class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-pink-100/70 text-pink-600 text-xs font-semibold mb-3">
      <Sparkles class="size-3.5" />
      <span>Pesan Dari Lubuk Hati</span>
    </div>

    <h2 class="text-3xl sm:text-4xl font-bold tracking-tight text-slate-900 mb-8">
      Surat Khusus Buat Kamu
    </h2>

    <!-- Closed Envelope State -->
    <div v-if="!isOpen" class="flex flex-col items-center">
      <Card
        class="w-full max-w-md p-8 cursor-pointer border-rose-200/80 bg-white/80 hover:bg-white hover:scale-[1.02] transition-all duration-300 shadow-md group"
        @click="toggleLetter"
      >
        <div class="size-16 rounded-full bg-rose-50 text-rose-500 mx-auto flex items-center justify-center mb-4 group-hover:scale-110 transition-transform">
          <Mail class="size-8" />
        </div>
        <h3 class="text-lg font-bold text-slate-800 mb-1">Ada satu surat yang belum kamu buka</h3>
        <p class="text-xs text-slate-500 mb-6">Sentuh untuk membuka pesan rahasia ini...</p>

        <Button class="w-full rounded-full shadow-rose-400/20 shadow-md">
          <Heart class="size-4 mr-1.5 fill-white text-white" />
          Buka Surat
        </Button>
      </Card>
    </div>

    <!-- Opened Letter State -->
    <div v-else class="animate-in fade-in zoom-in-95 duration-500">
      <Card class="p-8 sm:p-10 border-rose-200 bg-white/90 shadow-xl text-left relative overflow-hidden">
        <!-- Decorative subtle watermark -->
        <Heart class="absolute -right-8 -bottom-8 size-48 text-rose-100/40 pointer-events-none fill-rose-100/30" />

        <div class="flex items-center justify-between border-b border-rose-100 pb-4 mb-6">
          <div class="flex items-center gap-2">
            <Heart class="size-5 text-rose-500 fill-rose-500" />
            <span class="font-serif italic font-bold text-slate-800 text-lg">Untuk Pacarku Tersayang</span>
          </div>
          <span class="text-xs text-rose-500 font-medium bg-rose-50 px-2 py-0.5 rounded-full border border-rose-200/60">
            With Love
          </span>
        </div>

        <div class="space-y-4 text-slate-700 font-normal leading-relaxed text-sm sm:text-base">
          <p>
            Hai kamu,
          </p>
          <p>
            Aku membuat halaman web kecil ini karena kata-kata biasa terkadang tidak cukup untuk merangkum betapa berartinya kehadiranmu dalam hidupku.
          </p>
          <p>
            Terima kasih telah selalu ada—dengan tawamu yang menyenangkan, dukunganmu yang tak pernah berhenti, dan caramu membuat hal-hal sederhana terasa begitu istimewa.
          </p>
          <p>
            Semoga hari-harimu selalu dipenuhi kebahagiaan, sama seperti bagaimana kamu telah membahagiakan hari-hariku.
          </p>
          <p class="pt-4 font-serif italic text-right text-rose-600 font-semibold">
            — Dari seseorang yang selalu menyayangimu ❤️
          </p>
        </div>

        <div class="mt-8 pt-6 border-t border-rose-100 flex justify-center">
          <Button variant="outline" size="sm" class="rounded-full text-xs" @click="toggleLetter">
            Tutup Surat
          </Button>
        </div>
      </Card>
    </div>
  </section>
</template>
