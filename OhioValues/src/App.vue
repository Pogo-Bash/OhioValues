<script setup>
import { ref } from 'vue'
import Index from './components/index.vue'
import ValueCalculator from './components/ValueCalculator.vue'

// Simple state to track which page we're on
const currentPage = ref('home') // 'home' or 'calculator'

// Simple functions to switch pages
function showHome() {
  currentPage.value = 'home'
}

function showCalculator() {
  currentPage.value = 'calculator'
}
</script>

<template>
  <div>
    <!-- Show home page -->
    <Index 
      v-if="currentPage === 'home'" 
      :show-calculator="showCalculator"
    />
    
    <!-- Show calculator page -->
    <ValueCalculator 
      v-if="currentPage === 'calculator'" 
      :show-home="showHome"
    />
  </div>
</template>
