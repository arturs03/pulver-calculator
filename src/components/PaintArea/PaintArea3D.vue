<script lang="ts" setup>
import VInput from '@/components/ui/VInput.vue'
import { watch } from 'vue'
import { z } from 'zod'
import { toTypedSchema } from '@vee-validate/zod'
import { useForm } from 'vee-validate'

const triggerSubmit = defineModel<boolean>('triggerSubmit')
const area = defineModel<number>('area')

const schema = toTypedSchema(
  z.object({
    objectLength: z.number().min(0.1, 'Length must be greater than 0.1'),
    objectWidth: z.number().min(0.1, 'Width must be greater than 0.1'),
    objectHeight: z.number().min(0.1, 'Height must be greater than 0.1'),
  }),
)

const { handleSubmit } = useForm({
  validationSchema: schema,
  validateOnMount: false,
})

const emits = defineEmits(['formValues'])
const onSubmit = handleSubmit((formValues) => {
  const l = formValues.objectLength
  const w = formValues.objectWidth
  const h = formValues.objectHeight || 0

  area.value = 2 * (l * w + l * h + w * h)
  emits('formValues', formValues)
})

watch(triggerSubmit, (val) => {
  if (val) {
    onSubmit()
    triggerSubmit.value = false
  }
})
</script>
<template>
  <div class="space-y-4">
    <VInput label="Garums L, mm:" value-path="objectLength" />
    <VInput label="Platums W, mm:" value-path="objectWidth" />
    <VInput label="Augstums H, mm:" value-path="objectHeight" />
  </div>
</template>
