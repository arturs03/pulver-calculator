<template>
  <div class="container mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">PULVERKRĀSOŠANAS CENAS KALKULATORS</h1>
    <p class="mb-4">
      Cenas kalkulators aprēķinās pulverkrāsošanas izmaksas, balstoties uz sniegto informāciju.
    </p>
    <ol class="list-decimal list-inside mb-4">
      <li>Solis – nomēriet vajadzīgos izmērus un ierakstiet tos paredzētajās vietās.</li>
      <li>Aizpildiet nepieciešamo papildinformāciju un saņemiet cenu.</li>
    </ol>
    <p class="mb-4 font-bold">
      N.B.! Cena ir rēķināta, balstoties uz iesniegto informāciju. Lai saņemtu cenas apstiprinājumu
      vai arī neskaidru jautājumu gadījumā, augšupielādējiet detaļas foto vai rasējumu
    </p>

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

    <div class="card card-border border-base-300 card-md mb-8">
      <div class="card-body gap-4">
        <component
          :is="activeSection.component"
          v-model="formData[activeSection.key]"
          @update:model-value="updateFormData(activeSection.key, $event)"
        />
      </div>
    </div>
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
    <div class="mb-4">
      <h2 class="text-lg font-semibold mb-2">Komentāri</h2>
      <textarea
        v-model="formData.comments"
        class="w-full p-2 border rounded"
        rows="6"
        placeholder="Norādiet: Detaļas materiālu un esošo virsmas pārklājumu, Detaļas pielietojumu, Vai ir āra apstākļu iedarbība, Redzamās virsmas, Piekares punktus, Piegādes gadījumā adresi, kontaktpersonas t.nr., darba laiku, Citu svarīgu informāciju"
      ></textarea>
    </div>

    <div class="mt-4">
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
import { Form } from 'vee-validate'
import { z } from 'zod'
import { toFormValidator } from '@vee-validate/zod'

import PaintArea from '@/components/PaintArea.vue'
import IronThicknes from '@/components/IronThicknes.vue'
import OverlayType from '@/components/OverlayType.vue'
import Packaging from '@/components/Packaging.vue'
import PreProcessing from '@/components/PreProcessing.vue'
import Transportation from '@/components/Transportation.vue'

const schema = toFormValidator(
  z.object({
    PaintArea: z.number().min(0),
    IronThicknes: z.string(),
    Overlay: z.object({
      virsmasParkajums: z.array(z.string()),
      virsmasIzskats: z.string(),
    }),
    Packaging: z.string(),
    PreProcessing: z.string(),
    Transportation: z.array(z.string()),
    comments: z.string().optional(),
  }),
)

const sections = reactive([
  {
    title: 'KRĀSOŠANAS LAUKUMS',
    component: PaintArea,
    key: 'PaintArea',
    step: 1,
  },
  { title: 'METĀLA BIEZUMS', component: IronThicknes, key: 'IronThicknes', step: 2 },
  { title: 'PĀRKLĀJUMS', component: OverlayType, key: 'Overlay', step: 3 },
  { title: 'Packaging', component: Packaging, key: 'Packaging', step: 4 },
  { title: 'PRIEKŠAPSTRĀDE', component: PreProcessing, key: 'PreProcessing', step: 5 },
  { title: 'Transportation', component: Transportation, key: 'Transportation', step: 6 },
])

const activeSection = ref(sections[0])

const formData = reactive({
  PaintArea: 0,
  IronThicknes: '',
  Overlay: {
    virsmasParkajums: [],
    virsmasIzskats: '',
  },
  Packaging: '',
  PreProcessing: '',
  Transportation: [],
  comments: '',
})

const calculatedPrice = ref(null)

const toggleSection = (index) => {
  sections[index].isOpen = !sections[index].isOpen
}

const updateFormData = (key, value) => {
  formData[key] = value
}

const onSubmit = (values) => {
  // Šeit implementējiet cenas aprēķināšanas loģiku
  let price = 0

  // Krāsošanas laukuma cena
  price += formData.PaintArea * 10 // 10 EUR par m²

  // Metāla biezuma piemaksa
  if (formData.IronThicknes === '4 - 8 mm') price += 5
  else if (formData.IronThicknes === '9 – 15 mm') price += 10
  else if (formData.IronThicknes === '16 - 30 mm') price += 15

  // Pārklājuma cena
  price += formData.Overlay.virsmasParkajums.length * 20

  // Iepakojuma cena
  if (formData.Packaging === 'stretch_palete') price *= 1.08
  else if (formData.Packaging === 'stretch_palete_starplikas') price *= 1.15

  // Priekšapstrādes cena
  if (formData.PreProcessing === 'slipesana') price += formData.PaintArea * 15
  else if (formData.PreProcessing === 'struklošana') price += formData.PaintArea * 20

  // Transporta cena
  if (formData.Transportation.includes('panemsana')) price += 25
  if (formData.Transportation.includes('piegade')) price += 35

  calculatedPrice.value = price.toFixed(2)
}
</script>
