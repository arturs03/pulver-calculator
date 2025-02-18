<template>
  <div class="container mx-auto p-4 min-h-screen flex flex-col justify-center">
    <div class="w-full hidden md:flex justify-center">
      <ul class="steps mb-8">
        <li
          v-for="(section, index) in sections"
          :key="index"
          class="step"
          :class="{ 'step-primary': activeSection.step >= section.step }"
        >
          {{ section.title }}
        </li>
      </ul>
    </div>
    <div class="flex flex-col gap-2 items-center w-full md:hidden mb-5">
      <h2 class="text-xl font-bold mb-4">{{ activeSection.title }}</h2>
      <progress class="progress progress-primary" :value="progressPercentage" max="100" />
    </div>
    <div class="card card-border border-base-300 card-md mb-8 dark:bg-gray-300/10">
      <div class="card-body gap-4">
        <Transition name="step">
          <keep-alive>
            <component
              :is="activeSection.component"
              :trigger-emit="triggerEmit"
              @data="activeSection.callback"
            />
          </keep-alive>
        </Transition>
      </div>
    </div>
    <p v-if="errorMessage.length" class="text-red-400 my-4">{{ errorMessage }}</p>
    <div class="flex items-center gap-4 mb-10">
      <button v-if="canGoBack" class="btn btn-neutral" @click="goBack">Iepriekšējais</button>
      <button class="btn btn-primary ml-auto" @click="triggerEmit = true">
        {{ canGoNext ? 'Nākamais' : 'Aprēķināt cenu' }}
      </button>
    </div>
    <div v-if="calculatedPrice" class="mt-4 p-4 rounded">
      <h2 class="text-xl font-bold">Aprēķinātā cena: {{ calculatedPrice }} EUR</h2>
      <pre class="text-sm">
        {{ formData }}
      </pre>
    </div>
  </div>
</template>
<script lang="ts" setup>
import { ref, shallowRef, computed } from 'vue'
import PaintArea from '@/components/PaintArea/PaintArea.vue'
import IronThicknes from '@/components/IronThicknes.vue'
import OverlayType from '@/components/OverlayType.vue'
import Packaging from '@/components/Packaging.vue'
import PreProcessing from '@/components/PreProcessing.vue'
import Transportation from '@/components/Transportation.vue'
import type { IPaintAreaData, IOverlayType, ITransportation } from '@/components/types'

const sections = [
  {
    title: 'Krāsošanas laukums',
    component: PaintArea,
    key: 'paintArea',
    step: 1,
    callback: (data?: IPaintAreaData) => {
      triggerEmit.value = false
      if (!data?.area) {
        errorMessage.value = 'Laukums netika aprēķināts'

        return
      }

      formData.value.paintArea = data
      errorMessage.value = ''
      goNext()
    },
  },
  {
    title: 'Metāla biezums',
    component: IronThicknes,
    key: 'ironThicknes',
    step: 2,
    callback: (data?: string) => {
      triggerEmit.value = false
      if (!data) {
        errorMessage.value = 'Jāizvēlās krāsošanas laukums'

        return
      }

      formData.value.ironThicknes = data
      errorMessage.value = ''
      goNext()
    },
  },
  {
    title: 'Pārklājums',
    component: OverlayType,
    key: 'overlayType',
    step: 3,
    callback: (data?: IOverlayType) => {
      triggerEmit.value = false
      if (!data?.selectedOverlay) {
        errorMessage.value = 'Pārklājums netika aizpildīts'

        return
      }

      formData.value.overlayType = data
      errorMessage.value = ''
      goNext()
    },
  },
  {
    title: 'Iepakošana',
    component: Packaging,
    key: 'packaging',
    step: 4,
    callback: (data?: string) => {
      triggerEmit.value = false
      if (!data) {
        errorMessage.value = 'Jāizvēlās iepakošana'

        return
      }

      formData.value.packaging = data
      errorMessage.value = ''
      goNext()
    },
  },
  {
    title: 'Priekšapstrāde',
    component: PreProcessing,
    key: 'preProcessing',
    step: 5,
    callback: (data?: string) => {
      triggerEmit.value = false
      if (!data) {
        errorMessage.value = 'Jāizvēlās priekšapstrāde'

        return
      }

      formData.value.preProcessing = data
      errorMessage.value = ''
      goNext()
    },
  },
  {
    title: 'Piegāde',
    component: Transportation,
    key: 'transportation',
    step: 6,
    callback: (data?: ITransportation) => {
      triggerEmit.value = false
      if (!data?.selectedTransportation) {
        errorMessage.value = 'Jāizvēlās piegāde'

        return
      }

      formData.value.transportation = data.selectedTransportation
      formData.value.comments = data.comments
      errorMessage.value = ''

      calculatePrice()
    },
  },
]

const activeSection = shallowRef(sections[0])
const triggerEmit = ref(false)
const errorMessage = ref('')

const formData = ref({
  paintArea: {
    area: 0,
    dimmensions: {},
    figureType: '',
  },
  ironThicknes: '',
  overlayType: {
    selectedOverlay: '',
    selectedOverlayLook: '',
  },
  packaging: '',
  preProcessing: '',
  transportation: '',
  comments: '',
})

const MAX_STEPS = 6

const canGoBack = computed(() => activeSection.value.step > 1)
function goBack() {
  activeSection.value = sections[activeSection.value.step - 2]
}

const canGoNext = computed(() => activeSection.value.step < MAX_STEPS)
function goNext() {
  activeSection.value = sections[activeSection.value.step]
}

const calculatedPrice = ref<number | null>(null)
function calculatePrice() {
  calculatedPrice.value = 10000
}

const progressPercentage = computed(() => {
  const step = calculatedPrice.value ? MAX_STEPS : activeSection.value.step - 1
  return Math.round((step / MAX_STEPS) * 100)
})
</script>
<style scoped>
.step-enter-active {
  transition: all 0.5s ease 0.3s;
}

.step-leave-active {
  transition: all 0.2s ease;
  position: absolute;
}

.step-enter-from {
  opacity: 0;
  transform: translateX(100px);
}

.step-leave-to {
  opacity: 0;
  transform: translateX(-100px);
}
</style>
