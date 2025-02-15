<template>
  <div>
    <fieldset>
      <legend class="text-lg font-semibold mb-2">Ievadiet metāla maksimālo biezumu:</legend>
      <div class="space-y-2">
        <label v-for="option in options" :key="option" class="flex items-center space-x-2">
          <input type="radio" v-model="seelctedThickness" :value="option" class="radio" />
          <span>{{ option }}</span>
        </label>
      </div>
    </fieldset>
  </div>
</template>

<script lang="ts" setup>
import { ref, watch } from 'vue'

const options = ['< 3 mm', '4 - 8 mm', '9 – 15 mm', '16 - 30 mm']
const seelctedThickness = ref(null)

const props = defineProps<{
  triggerEmit: boolean
}>()

const emits = defineEmits(['data'])
watch(
  () => props.triggerEmit,
  (val) => {
    if (val) {
      emits('data', seelctedThickness.value)
    }
  },
)
</script>
