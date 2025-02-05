<template>
  <div>
    <fieldset>
      <legend class="text-lg font-semibold mb-2">Nepieciešamā transportēšana:</legend>
      <div class="space-y-2">
        <label v-for="option in options" :key="option.value" class="flex items-center space-x-2">
          <input
            type="checkbox"
            v-model="selectedTransports"
            :value="option.value"
            class="form-checkbox"
          />
          <span>{{ option.label }}</span>
        </label>
      </div>
    </fieldset>
    <div class="mt-4 text-sm text-gray-600">
      <p>N.B. Pulverkraso.lv nodrošina transportu darba dienās 2 x nedēļā.</p>
      <p>
        Pulverkraso.lv nodrošina iekraušanu/izkraušanu savā teritorijā, bet Pasūtītājs nodrošina
        iekraušanu/izkraušanu savā teritorijā.
      </p>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'

const options = [
  { value: 'panemsana', label: 'Paņemšana no pasūtītāja (25 eur/reiss Rīgas robežās)' },
  { value: 'piegade', label: 'Piegāde pasūtītājam (35 eur/reiss Rīgas robežās)' },
]

const selectedTransports = ref([])
</script>
