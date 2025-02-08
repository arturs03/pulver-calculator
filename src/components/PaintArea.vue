<template>
  <div>
    <fieldset class="mb-4">
      <legend class="text-lg font-semibold mb-2">Krāsojamās detaļas figūra:</legend>
      <div class="space-y-2">
        <label class="flex items-center space-x-2">
          <input type="radio" v-model="figuraType" value="plakne" class="radio" />
          <span>Plakne - 2D objekts</span>
          <Info
            data="Plakne - 2D objekts (plakana detaļa, flancis, metāla loksne utt.). Krāsošanas plakni rēķina no abām pusēm."
          />
        </label>
        <label class="flex items-center space-x-2">
          <input type="radio" v-model="figuraType" value="telpa" class="radio" />
          <span>Telpa - 3D objekts</span>
          <Info
            data="Telpa - 3D objekts (riteņa disks, lode utt.). Kārbveida detaļai rēķina ārējo plakni."
          />
        </label>
        <label class="flex items-center space-x-2">
          <input type="radio" v-model="figuraType" value="rezgis" class="radio" />
          <span>Režģis</span>
          <Info
            data="Režģis (žogs, vārti, balkons, siets, velo rāmis,  utt.). Aprēķinā ņem vērā profilu apkārtmēru S un profilu garumu L."
          />
        </label>
      </div>
    </fieldset>

    <div v-if="figuraType === 'plakne'" class="space-y-4">
      <div>
        <label for="garums-plakne" class="block text-sm font-medium">Garums L, mm:</label>
        <input id="garums-plakne" v-model.number="garums" type="number" class="input" />
      </div>
      <div>
        <label for="platums-plakne" class="block text-sm font-medium">Platums W, mm:</label>
        <input id="platums-plakne" v-model.number="platums" type="number" class="input" />
      </div>
    </div>

    <div v-if="figuraType === 'telpa'" class="space-y-4">
      <div>
        <label for="garums-telpa" class="block text-sm font-medium">Garums L, mm:</label>
        <input id="garums-telpa" v-model.number="garums" type="number" class="input" />
      </div>
      <div>
        <label for="platums-telpa" class="block text-sm font-medium">Platums W, mm:</label>
        <input id="platums-telpa" v-model.number="platums" type="number" class="input" />
      </div>
      <div>
        <label for="augstums-telpa" class="block text-sm font-medium">Augstums H, mm:</label>
        <input id="augstums-telpa" v-model.number="augstums" type="number" class="input" />
      </div>
    </div>

    <div v-if="figuraType === 'rezgis'" class="space-y-4">
      <fieldset>
        <legend class="text-sm font-medium mb-2">Profila apkārtmērs S:</legend>
        <div class="space-y-2">
          <label v-for="s in [10, 20, 30, 40, 50]" :key="s" class="flex flex-col space-x-2">
            <div class="flex gap-2 mb-1">
              <p class="contents text-sm font-medium">S={{ s }}mm</p>
              <Info :data="getSInfo(s)" />
            </div>
            <input
              type="number"
              class="input validator"
              required
              title="Must be between be 1 to 10"
            />
          </label>
        </div>
      </fieldset>
    </div>

    <button
      @click="calculateArea"
      class="mt-4 px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50"
    >
      Aprēķināt laukumu
    </button>

    <p v-if="area" class="mt-4 text-lg font-semibold">Aprēķinātais laukums: {{ area }} m²</p>
  </div>
</template>

<script setup lang="ts">
import Info from '@/components/ui/Info.vue'
import { ref } from 'vue'

const figuraType = ref('plakne')
const garums = ref(0)
const platums = ref(0)
const augstums = ref(0)
const selectedS = ref(10)
const showInfo = ref(null)
const area = ref(null)

const getSInfo = (s) => {
  const info = {
    10: 'Apaļš profils D<10 mm, Taisnstūra profils 10x10 mm, 5x15 mm',
    20: 'Apaļš profils D=10-20 mm, Taisnstūra profils 20x20 mm, 15x25 mm, 10x30 mm',
    30: 'Apaļš profils D=20-30 mm, Taisnstūra profils 30x30 mm, 20x40 mm, 10x50 mm',
    40: 'Apaļš profils D=30-40 mm, Taisnstūra profils 40x40 mm, 30x50 mm, 20x60 mm, 10x70 mm',
    50: 'Apaļš profils D=40-50 mm, Taisnstūra profils 50x50 mm, 40x60 mm, 30x70 mm, 20x80 mm, 10x90 mm',
  }
  return info[s]
}

const calculateArea = () => {
  let result = 0
  if (figuraType.value === 'plakne') {
    result = (garums.value * platums.value * 2) / 1000000 // Abas puses
  } else if (figuraType.value === 'telpa') {
    result =
      (2 *
        (garums.value * platums.value +
          garums.value * augstums.value +
          platums.value * augstums.value)) /
      1000000
  } else if (figuraType.value === 'rezgis') {
    // Šeit vajadzētu ieviest papildu loģiku režģa laukuma aprēķināšanai
    result = selectedS.value / 1000 // Vienkāršots piemērs
  }
  area.value = result.toFixed(2)
}
</script>
