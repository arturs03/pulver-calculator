<template>
  <div class="space-y-6">
    <fieldset>
      <legend class="text-lg font-semibold mb-2">Nepieciešamie virsmas pārklājumi:</legend>
      <div class="space-y-2">
        <label
          v-for="option in parklajumaOpcijas"
          :key="option"
          class="flex items-center space-x-2"
        >
          <input type="radio" v-model="selectedOverlay" :value="option" class="radio" />
          <span>{{ option }}</span>
        </label>
      </div>
    </fieldset>
    <fieldset>
      <legend class="text-lg font-semibold mb-2">Nepieciešamais virsmas izskats:</legend>
      <div class="space-y-2">
        <label
          v-for="option in virsmasIzskataOpcijas"
          :key="option"
          class="flex items-center space-x-2"
        >
          <input type="radio" v-model="selectedOverlayLook" :value="option" class="radio" />
          <span>{{ option }}</span>
        </label>
      </div>
    </fieldset>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'

const props = defineProps<{
  triggerEmit: boolean
}>()

const parklajumaOpcijas = [
  'EPOXY grunts',
  'ZN grunts',
  'Krāsa RAL',
  'Metālika / Metallica',
  'Caurspīdīgs / Transparent',
]
const virsmasIzskataOpcijas = [
  'Glancēts',
  'Matēts 80%',
  "Struktūra 'smilšpapīrs'",
  "Struktūra 'apelsīna miza'",
]

const selectedOverlay = ref('')
const selectedOverlayLook = ref('')

const emits = defineEmits(['data'])
watch(
  () => props.triggerEmit,
  (val) => {
    if (val) {
      emits('data', {
        selectedOverlay: selectedOverlay.value,
        selectedOverlayLook: selectedOverlayLook.value,
      })
    }
  },
)
</script>
