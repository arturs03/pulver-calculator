<template>
  <form @submit.prevent="">
    <fieldset class="mb-4">
      <legend class="text-lg font-semibold mb-2">Krāsojamās detaļas figūra:</legend>
      <div class="space-y-2">
        <label class="flex items-center space-x-2">
          <input type="radio" v-model="figureType" :value="OPTION_2D" class="radio" />
          <span>Plakne - 2D objekts</span>
          <Info
            data="Plakne - 2D objekts (plakana detaļa, flancis, metāla loksne utt.). Krāsošanas plakni rēķina no abām pusēm."
          />
        </label>
        <label class="flex items-center space-x-2">
          <input type="radio" v-model="figureType" :value="OPTION_3D" class="radio" />
          <span>Telpa - 3D objekts</span>
          <Info
            data="Telpa - 3D objekts (riteņa disks, lode utt.). Kārbveida detaļai rēķina ārējo plakni."
          />
        </label>
        <label class="flex items-center space-x-2">
          <input type="radio" v-model="figureType" :value="OPTION_ND" class="radio" />
          <span>Režģis</span>
          <Info
            data="Režģis (žogs, vārti, balkons, siets, velo rāmis,  utt.). Aprēķinā ņem vērā profilu apkārtmēru S un profilu garumu L."
          />
        </label>
      </div>
    </fieldset>
    <PaintArea2D
      v-if="figureType === OPTION_2D"
      v-model:area="area"
      v-model:trigger-submit="triggerSubmit"
      @form-values="setFormValues"
    />
    <PaintArea3D
      v-else-if="figureType === OPTION_3D"
      v-model:area="area"
      v-model:trigger-submit="triggerSubmit"
      @form-values="setFormValues"
    />
    <PaintAreaND
      v-else-if="figureType === OPTION_ND"
      v-model:area="area"
      v-model:trigger-submit="triggerSubmit"
      @form-values="setFormValues"
    />
    <button type="submit" class="mt-4 btn btn-info" @click="triggerSubmit = true">
      Aprēķināt laukumu
    </button>
    <p v-if="area" class="mt-4 font-semibold">Aprēķinātais laukums: {{ area }} mm²</p>
  </form>
</template>
<script setup lang="ts">
import Info from '@/components/ui/Info.vue'
import PaintArea2D from '@/components/PaintArea/PaintArea2D.vue'
import PaintArea3D from '@/components/PaintArea/PaintArea3D.vue'
import PaintAreaND from '@/components/PaintArea/PaintAreaND.vue'
import { ref, watch } from 'vue'

const props = defineProps<{
  triggerEmit: boolean
}>()

const OPTION_2D = '2d'
const OPTION_3D = '3d'
const OPTION_ND = 'nd'

const figureType = ref(OPTION_2D)
const triggerSubmit = ref(false)
const area = ref(0)
const formValues = ref<object | null>(null)
function setFormValues(values: object) {
  formValues.value = values
}

watch(figureType, (newVal, oldVal) => {
  if (newVal !== oldVal) {
    area.value = 0
  }
})

const emits = defineEmits(['data'])
watch(
  () => props.triggerEmit,
  (val) => {
    if (val) {
      emits('data', {
        area: area.value,
        dimmensions: formValues.value,
        figureType: figureType.value,
      })
    }
  },
)
</script>
