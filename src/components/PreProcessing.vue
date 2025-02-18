<template>
  <div>
    <fieldset>
      <legend class="text-lg font-semibold mb-2">Nepieciešamā priekšapstrāde:</legend>
      <div class="space-y-2">
        <label v-for="option in options" :key="option.value" class="flex items-center space-x-2">
          <input type="radio" v-model="selectedPreProcesing" :value="option.value" class="radio" />
          <span>{{ option.label }}</span>
        </label>
      </div>
    </fieldset>
    <p class="mt-2 text-sm text-gray-600">
      N.B. Karstā cinka slīpēšana negarantē pilnīgu plaknes gludumu metinājuma vietās!
    </p>
  </div>
</template>
<script lang="ts" setup>
import { ref, watch } from 'vue'

const props = defineProps<{
  triggerEmit: boolean
}>()

const options = [
  {
    value: 'slipesana',
    label: 'Slīpēšana (Zn notecējumi) + virsmas raupjināšana G180 (15 eur/m²)',
  },
  { value: 'struklošana', label: 'Strūklošana Sa2½ (20 eur/m²)' },
]

const selectedPreProcesing = ref('')

const emits = defineEmits(['data'])
watch(
  () => props.triggerEmit,
  (val) => {
    if (val) {
      emits('data', selectedPreProcesing.value)
    }
  },
)
</script>
