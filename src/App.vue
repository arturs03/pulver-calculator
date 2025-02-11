<script setup lang="ts">
import { ref } from 'vue'
import Calculator from '@/components/Calculator.vue'

const isCalculatorView = ref(false)
</script>

<template>
  <main>
    <div
      v-if="!isCalculatorView"
      class="container mx-auto flex flex-col items-center justify-center h-screen"
    >
      <h1 class="text-2xl font-bold mb-4">PULVERKRĀSOŠANAS CENAS KALKULATORS</h1>
      <p class="mb-4">
        Cenas kalkulators aprēķinās pulverkrāsošanas izmaksas, balstoties uz sniegto informāciju.
      </p>
      <ol class="list-decimal list-inside mb-4">
        <li>Solis – nomēriet vajadzīgos izmērus un ierakstiet tos paredzētajās vietās.</li>
        <li>Aizpildiet nepieciešamo papildinformāciju un saņemiet cenu.</li>
      </ol>
      <p class="mb-4 font-bold">
        N.B.! Cena ir rēķināta, balstoties uz iesniegto informāciju. Lai saņemtu cenas
        apstiprinājumu vai arī neskaidru jautājumu gadījumā, augšupielādējiet detaļas foto vai
        rasējumu
      </p>
      <button class="btn btn-primary mx-auto" @click="isCalculatorView = true">Kalkulātors</button>
    </div>
    <Calculator v-else />
  </main>
</template>
