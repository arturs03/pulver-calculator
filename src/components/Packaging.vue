<template>
  <div>
    <fieldset>
      <legend class="text-lg font-semibold mb-2">Nepieciešamais iepakošanas veids:</legend>
      <div class="space-y-2">
        <label v-for="option in options" :key="option.value" class="flex items-center space-x-2">
          <input
            type="radio"
            v-model="selectedIepakojums"
            :value="option.value"
            class="form-radio"
          />
          <span>{{ option.label }}</span>
        </label>
      </div>
    </fieldset>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue'

const options = [
  { value: 'stretch', label: "'Stretch' plēve (bezmaksas)" },
  { value: 'stretch_palete', label: "'Stretch' plēve + iepakošanas uz paletes (+8%)" },
  {
    value: 'stretch_palete_starplikas',
    label:
      "'Stretch' plēve + iepakošanas uz paletes + starplikas – transportējot aizsargā pret savstarpēju saskaršanos (+15%)",
  },
]

const selectedIepakojums = ref('stretch')
</script>
