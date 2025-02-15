<template>
  <div>
    <fieldset class="mb-4">
      <legend class="text-lg font-semibold mb-2">Nepieciešamā transportēšana:</legend>
      <div class="space-y-2">
        <label v-for="option in options" :key="option.value" class="flex items-center space-x-2">
          <input
            type="radio"
            v-model="selectedTransportation"
            :value="option.value"
            class="radio"
          />
          <span>{{ option.label }}</span>
        </label>
      </div>
    </fieldset>
    <div class="mb-4 text-sm text-gray-400">
      <p>N.B. Pulverkraso.lv nodrošina transportu darba dienās 2 x nedēļā.</p>
      <p>
        Pulverkraso.lv nodrošina iekraušanu/izkraušanu savā teritorijā, bet Pasūtītājs nodrošina
        iekraušanu/izkraušanu savā teritorijā.
      </p>
    </div>
    <div>
      <h2 class="text-lg font-semibold mb-2">Komentāri</h2>
      <textarea
        v-model="comments"
        class="w-full p-2 border rounded"
        rows="6"
        placeholder="Norādiet: Detaļas materiālu un esošo virsmas pārklājumu, Detaļas pielietojumu, Vai ir āra apstākļu iedarbība, Redzamās virsmas, Piekares punktus, Piegādes gadījumā adresi, kontaktpersonas t.nr., darba laiku, Citu svarīgu informāciju"
      ></textarea>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, watch } from 'vue'

const props = defineProps<{
  triggerEmit: boolean
}>()

const options = [
  { value: 'panemsana', label: 'Paņemšana no pasūtītāja (25 eur/reiss Rīgas robežās)' },
  { value: 'piegade', label: 'Piegāde pasūtītājam (35 eur/reiss Rīgas robežās)' },
]

const selectedTransportation = ref('')
const comments = ref('')

const emits = defineEmits(['data'])
watch(
  () => props.triggerEmit,
  (val) => {
    if (val) {
      emits('data', {
        selectedTransportation: selectedTransportation.value,
        comments: comments.value,
      })
    }
  },
)
</script>
