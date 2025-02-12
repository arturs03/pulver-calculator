<template>
  <div class="container mx-auto p-4 h-screen flex flex-col justify-center">
    <div class="w-full flex justify-center">
      <ul class="steps mb-8">
        <li
          v-for="(section, index) in sections"
          :key="index"
          class="step"
          :class="{ 'step-primary': activeSection.step >= section.step }"
          @click="activeSection = section"
        >
          {{ section.title }}
        </li>
      </ul>
    </div>

    <TransitionGroup name="list">
      <div class="card card-border border-base-300 card-md mb-8">
        <div class="card-body gap-4">
          <component :is="activeSection.component" />
        </div>
      </div>
    </TransitionGroup>
    <div class="flex items-center gap-4 mb-10">
      <button
        v-if="activeSection.step > 1"
        class="btn btn-neutral"
        @click="activeSection = sections[activeSection.step - 2]"
      >
        Iepriekšējais
      </button>
      <button
        v-if="activeSection.step < 6"
        class="btn btn-primary ml-auto"
        @click="activeSection = sections[activeSection.step]"
      >
        Nākamais
      </button>
    </div>
    <div v-if="activeSection.step === 6" class="mt-4">
      <button
        type="submit"
        class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50"
      >
        Aprēķināt cenu
      </button>
    </div>
    <div v-if="calculatedPrice" class="mt-4 p-4 bg-green-100 border border-green-400 rounded">
      <h2 class="text-xl font-bold">Aprēķinātā cena: {{ calculatedPrice }} EUR</h2>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, reactive } from 'vue'

import PaintArea from '@/components/PaintArea/PaintArea.vue'
import IronThicknes from '@/components/IronThicknes.vue'
import OverlayType from '@/components/OverlayType.vue'
import Packaging from '@/components/Packaging.vue'
import PreProcessing from '@/components/PreProcessing.vue'
import Transportation from '@/components/Transportation.vue'

const sections = reactive([
  {
    title: 'Krāsošanas laukums',
    component: PaintArea,
    key: 'PaintArea',
    step: 1,
  },
  { title: 'Metāla biezums', component: IronThicknes, key: 'IronThicknes', step: 2 },
  { title: 'Pārklājums', component: OverlayType, key: 'Overlay', step: 3 },
  { title: 'Iepakošana', component: Packaging, key: 'Packaging', step: 4 },
  { title: 'Priekšapstrāde', component: PreProcessing, key: 'PreProcessing', step: 5 },
  { title: 'Piegāde', component: Transportation, key: 'Transportation', step: 6 },
])

const activeSection = ref(sections[0])

// const formData = reactive({
//   paintArea: 0,
//   IronThicknes: '',
//   overlay: {
//     type: [],
//     visual: '',
//   },
//   packaging: '',
//   preProcessing: '',
//   transportation: [],
//   comments: '',
// })

const calculatedPrice = ref(null)
</script>
<style scoped>
.list-enter-active {
  transition: all 0.5s ease 0.3s;
}

.list-leave-active {
  transition: all 0.3s ease;
  position: absolute;
  width: 100%;
}

.list-enter-from {
  opacity: 0;
  transform: translateX(100px);
}

.list-leave-to {
  opacity: 0;
  transform: translateX(-100px);
}
</style>
