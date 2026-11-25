<script setup lang="ts">
import { type HTMLAttributes } from 'vue';
import { cn } from '@/lib/utils';

interface Props {
  class?: HTMLAttributes['class'];
}

const props = defineProps<Props>();
</script>

<template>
  <div
    :class="
      cn(
        'rounded-2xl border border-rose-100/80 bg-white/70 backdrop-blur-md shadow-sm transition-all duration-300 hover:shadow-md hover:border-rose-200',
        props.class
      )
    "
  >
    <slot />
  </div>
</template>
