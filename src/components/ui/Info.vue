<script setup lang="ts">
import InfoCircle from '@/components/icons/InfoCircle.vue'

defineProps<{ data: string }>()
</script>
<template>
  <div class="tooltip cursor-pointer" :data-tip="data">
    <InfoCircle />
  </div>
</template>
