<script lang="ts" setup>
import { defineProps } from 'vue'
import { useField } from 'vee-validate'

const props = defineProps<{ label?: string; valuePath: string }>()
const { value, errorMessage } = useField<number>(props.valuePath)
</script>
<template>
  <div class="flex flex-col gap-2">
    <label :for="valuePath" class="text-sm font-medium flex items-center gap-2">
      <slot v-if="!label?.length" name="label" />
      <template v-else>
        {{ label }}
      </template>
    </label>
    <input
      :id="valuePath"
      v-model="value"
      type="number"
      :class="['input w-full md:w-1/4', { 'input-error': errorMessage?.length }]"
    />
    <span v-if="errorMessage?.length" class="text-red-500 text-sm">{{ errorMessage }}</span>
  </div>
</template>
