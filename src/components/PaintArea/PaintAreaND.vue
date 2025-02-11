<script lang="ts" setup>
import VInput from '@/components/ui/VInput.vue'
import Info from '@/components/ui/Info.vue'
import { defineModel, watch } from 'vue'
import { z } from 'zod'
import { toTypedSchema } from '@vee-validate/zod'
import { useForm } from 'vee-validate'

const triggerSubmit = defineModel<boolean>('triggerSubmit')
const area = defineModel<number>('area')

const sOptions = [10, 20, 30, 40, 50] as const
const info = {
  10: 'Apaļš profils D<10 mm, Taisnstūra profils 10x10 mm, 5x15 mm',
  20: 'Apaļš profils D=10-20 mm, Taisnstūra profils 20x20 mm, 15x25 mm, 10x30 mm',
  30: 'Apaļš profils D=20-30 mm, Taisnstūra profils 30x30 mm, 20x40 mm, 10x50 mm',
  40: 'Apaļš profils D=30-40 mm, Taisnstūra profils 40x40 mm, 30x50 mm, 20x60 mm, 10x70 mm',
  50: 'Apaļš profils D=40-50 mm, Taisnstūra profils 50x50 mm, 40x60 mm, 30x70 mm, 20x80 mm, 10x90 mm',
}

const getTooltipInfo = (s: keyof typeof info) => {
  if (s in info) {
    return info[s]
  }

  return ''
}

const schema = toTypedSchema(
  z.object({
    profiles: z
      .array(z.number().min(1, 'Must be greater than 0').max(10, 'Must be less than 10'))
      .min(1, 'At least one profile is required'),
  }),
)

const { handleSubmit } = useForm({
  validationSchema: schema,
  validateOnMount: false,
  initialValues: {
    profiles: Array(sOptions.length).fill(0),
  },
})

const onSubmit = handleSubmit((formValues) => {
  const totalLength = formValues.profiles.reduce((sum, count, index) => {
    const perimeter = sOptions[index]
    return sum + count * perimeter
  }, 0)
  area.value = totalLength / 1000
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
    <fieldset>
      <legend class="text-sm font-medium mb-2">Profila apkārtmērs S:</legend>
      <div class="space-y-2">
        <VInput v-for="(s, i) in sOptions" :key="s" :value-path="`profiles[${i}]`">
          <template #label>
            <p class="contents text-sm font-medium">S={{ s }}mm</p>
            <Info :data="getTooltipInfo(s)" />
          </template>
        </VInput>
      </div>
    </fieldset>
  </div>
</template>
