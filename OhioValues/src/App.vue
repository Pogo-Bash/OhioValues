<script setup>
import { ref } from 'vue'
import Index from './components/index.vue'
import ValueCalculator from './components/ValueCalculator.vue'

const currentView = ref('home') // 'home' or 'calculator'

const switchToHome = () => {
  currentView.value = 'home'
}

const switchToCalculator = () => {
  currentView.value = 'calculator'
}
</script>

<template>
  <div>
    <!-- Pass navigation functions as props to both components -->
    <Index 
      v-if="currentView === 'home'" 
      @switch-to-calculator="switchToCalculator"
    />
    <ValueCalculator 
      v-if="currentView === 'calculator'" 
      @switch-to-home="switchToHome"
    />
  </div>
</template>
