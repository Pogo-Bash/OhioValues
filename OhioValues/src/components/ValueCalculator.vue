<script setup>
import { ref, computed, onMounted } from 'vue';

const isMenuOpen = ref(false);
const showAddModal = ref(false);
const currentSide = ref('left'); // 'left' or 'right'

const leftSide = ref([]);
const rightSide = ref([]);

const searchQuery = ref('');
const selectedCategory = ref('');
const selectedAvailability = ref('');

const defaultWeapons = [
  // === UNOBTAINABLE SKINS (HIGHEST TIER) ===
  
  // Invisible skins - Ultra rare
  { id: 1, category: 'Invisible', weapon: 'Aug', price: '20m', demand: '2/10', availability: 'Unobtainable' },
  { id: 2, category: 'Invisible', weapon: 'RPG', price: '18m', demand: '8/10', availability: 'Unobtainable' },
  { id: 3, category: 'Invisible', weapon: 'Barrett', price: '17m', demand: '5/10', availability: 'Unobtainable' },
  { id: 4, category: 'Invisible', weapon: 'AS Val', price: '15m', demand: '7/10', availability: 'Unobtainable' },
  { id: 5, category: 'Invisible', weapon: 'P90', price: '15m', demand: '7/10', availability: 'Unobtainable' },
  { id: 6, category: 'Invisible', weapon: 'Scar', price: '15m', demand: '7/10', availability: 'Unobtainable' },
  { id: 7, category: 'Invisible', weapon: 'Riot Shield', price: '13m', demand: '7/10', availability: 'Unobtainable' },
  { id: 8, category: 'Invisible', weapon: 'M1', price: '9m', demand: '3/10', availability: 'Unobtainable' },
  { id: 9, category: 'Invisible', weapon: 'Flamethrower', price: '6m', demand: '2/10', availability: 'Unobtainable' },
  { id: 10, category: 'Invisible', weapon: 'Python', price: '5m', demand: '3/10', availability: 'Unobtainable' },
  
  // Obsidian skins - Very rare
  { id: 11, category: 'Obsidian', weapon: 'Minigun', price: '15m', demand: '6/10', availability: 'Unobtainable' },
  { id: 12, category: 'Obsidian', weapon: 'FNFAL', price: '12.5m', demand: '8/10', availability: 'Unobtainable' },
  { id: 13, category: 'Obsidian', weapon: 'RPG', price: '12m', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 14, category: 'Obsidian', weapon: 'AS Val', price: '10m', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 15, category: 'Obsidian', weapon: 'Aug', price: '9m', demand: '7/10', availability: 'Unobtainable' },
  { id: 16, category: 'Obsidian', weapon: 'Barrett', price: '9m', demand: '4/10', availability: 'Unobtainable' },
  { id: 17, category: 'Obsidian', weapon: 'P90', price: '9m', demand: '7/10', availability: 'Unobtainable' },
  { id: 18, category: 'Obsidian', weapon: 'Scar', price: '7m', demand: '7/10', availability: 'Unobtainable' },
  { id: 19, category: 'Obsidian', weapon: 'AK-47', price: '6m', demand: '5/10', availability: 'Unobtainable' },
  { id: 20, category: 'Obsidian', weapon: 'M1', price: '6m', demand: '6/10', availability: 'Unobtainable' },
  
  // Void skins - High tier
  { id: 21, category: 'Void', weapon: 'RPG', price: '8.5m', demand: '10/10', availability: 'Unobtainable' },
  { id: 22, category: 'Void', weapon: 'AS VAL', price: '6.8m', demand: '9/10', availability: 'Unobtainable' },
  { id: 23, category: 'Void', weapon: 'AUG', price: '6.5m', demand: '9/10', availability: 'Unobtainable' },
  { id: 24, category: 'Void', weapon: 'M4A1', price: '5.4m', demand: '7/10', availability: 'Unobtainable' },
  { id: 25, category: 'Void', weapon: 'Barrett', price: '5.2m', demand: '7/10', availability: 'Unobtainable' },
  { id: 26, category: 'Void', weapon: 'AK-47', price: '4m', demand: '7/10', availability: 'Unobtainable' },
  { id: 27, category: 'Void', weapon: 'Tommy Gun', price: '3.3m', demand: '7/10', availability: 'Unobtainable' },
  
  // Cyberpunk skins - Popular high tier
  { id: 28, category: 'Cyberpunk', weapon: 'AS VAL', price: '4.7m', demand: '10/10', availability: 'Unobtainable' },
  { id: 29, category: 'Cyberpunk', weapon: 'AUG', price: '4.5m', demand: '10/10', availability: 'Unobtainable' },
  { id: 30, category: 'Cyberpunk', weapon: 'M4A1', price: '3.7m', demand: '8.5/10', availability: 'Unobtainable' },
  { id: 31, category: 'Cyberpunk', weapon: 'AK-47', price: '3m', demand: '7/10', availability: 'Unobtainable' },
  { id: 32, category: 'Cyberpunk', weapon: 'Tommy', price: '2.8m', demand: '7.5/10', availability: 'Unobtainable' },
  
  // Solid Gold skins
  { id: 33, category: 'Solid Gold', weapon: 'RPG', price: '3.9m', demand: '9/10', availability: 'Unobtainable' },
  { id: 34, category: 'Solid Gold', weapon: 'AS VAL', price: '3.6m', demand: '7/10', availability: 'Unobtainable' },
  { id: 35, category: 'Solid Gold', weapon: 'AUG', price: '3.4m', demand: '7/10', availability: 'Unobtainable' },
  { id: 36, category: 'Solid Gold', weapon: 'Barrett', price: '3.3m', demand: '6/10', availability: 'Unobtainable' },
  { id: 37, category: 'Solid Gold', weapon: 'M4A1', price: '3.2m', demand: '6/10', availability: 'Unobtainable' },
  
  // Frozen Diamond skins
  { id: 38, category: 'Frozen Diamond', weapon: 'RPG', price: '1.5m', demand: '8/10', availability: 'Unobtainable' },
  { id: 39, category: 'Frozen Diamond', weapon: 'Scar L', price: '1.4m', demand: '7/10', availability: 'Unobtainable' },
  { id: 40, category: 'Frozen Diamond', weapon: 'AUG', price: '1.3m', demand: '6/10', availability: 'Unobtainable' },
  { id: 41, category: 'Frozen Diamond', weapon: 'P90', price: '1m', demand: '6/10', availability: 'Unobtainable' },
  { id: 42, category: 'Frozen Diamond', weapon: 'MP7', price: '900k', demand: '5/10', availability: 'Unobtainable' },
  
  // Tactical skins
  { id: 43, category: 'Tactical', weapon: 'Scar L', price: '1.6m', demand: '10/10', availability: 'Unobtainable' },
  { id: 44, category: 'Tactical', weapon: 'AUG', price: '800k', demand: '9/10', availability: 'Unobtainable' },
  { id: 45, category: 'Tactical', weapon: 'AS VAL', price: '750k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 46, category: 'Tactical', weapon: 'Barrett', price: '600k', demand: '6/10', availability: 'Unobtainable' },
  { id: 47, category: 'Tactical', weapon: 'MP7', price: '530k', demand: '7/10', availability: 'Unobtainable' },
  
  // Amethyst skins (Atomic)
  { id: 48, category: 'Amethyst', weapon: 'RPG', price: '1.2m', demand: '9.5/10', availability: 'Unobtainable' },
  { id: 49, category: 'Amethyst', weapon: 'AS Val', price: '950k', demand: '8.5/10', availability: 'Unobtainable' },
  { id: 50, category: 'Amethyst', weapon: 'M4A1', price: '850k', demand: '8/10', availability: 'Unobtainable' },
  { id: 51, category: 'Amethyst', weapon: 'Sawn Off', price: '750k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 52, category: 'Amethyst', weapon: 'Double Barrel', price: '700k', demand: '7/10', availability: 'Unobtainable' },
  
  // === OBTAINABLE SKINS ===
  
  // Dark Matter skins - Most common obtainable
  { id: 53, category: 'Dark Matter', weapon: 'Scar L', price: '300k', demand: '8/10', availability: 'Obtainable' },
  { id: 54, category: 'Dark Matter', weapon: 'P90', price: '260k', demand: '7/10', availability: 'Obtainable' },
  { id: 55, category: 'Dark Matter', weapon: 'RPG', price: '250k', demand: '6/10', availability: 'Obtainable' },
  { id: 56, category: 'Dark Matter', weapon: 'As Val', price: '200k', demand: '6/10', availability: 'Obtainable' },
  { id: 57, category: 'Dark Matter', weapon: 'Aug', price: '200k', demand: '6.5/10', availability: 'Obtainable' },
  { id: 58, category: 'Dark Matter', weapon: 'Barrett M107', price: '190k', demand: '4/10', availability: 'Obtainable' },
  { id: 59, category: 'Dark Matter', weapon: 'M4A1', price: '170k', demand: '5/10', availability: 'Obtainable' },
  { id: 60, category: 'Dark Matter', weapon: 'AK-47', price: '170k', demand: '4/10', availability: 'Obtainable' },
  
  // Anti Matter skins - Obtainable
  { id: 61, category: 'Anti Matter', weapon: 'Scar L', price: '280k', demand: '7.5/10', availability: 'Obtainable' },
  { id: 62, category: 'Anti Matter', weapon: 'P90', price: '240k', demand: '7/10', availability: 'Obtainable' },
  { id: 63, category: 'Anti Matter', weapon: 'RPG', price: '230k', demand: '6/10', availability: 'Obtainable' },
  { id: 64, category: 'Anti Matter', weapon: 'As Val', price: '180k', demand: '6/10', availability: 'Obtainable' },
  { id: 65, category: 'Anti Matter', weapon: 'Aug', price: '180k', demand: '6.5/10', availability: 'Obtainable' },
  
  // Crimson Blood skins - Obtainable
  { id: 66, category: 'Crimson Blood', weapon: 'Scar L', price: '320k', demand: '8/10', availability: 'Obtainable' },
  { id: 67, category: 'Crimson Blood', weapon: 'Aug', price: '275k', demand: '7.5/10', availability: 'Obtainable' },
  { id: 68, category: 'Crimson Blood', weapon: 'As Val', price: '245k', demand: '7/10', availability: 'Obtainable' },
  { id: 69, category: 'Crimson Blood', weapon: 'M4A1', price: '220k', demand: '6.5/10', availability: 'Obtainable' },
  { id: 70, category: 'Crimson Blood', weapon: 'AK-47', price: '195k', demand: '5/10', availability: 'Obtainable' },
  
  // === LIMITED TIME SKINS ===
  
  // Easter Egg skins - Event limited
  { id: 71, category: 'Easter Egg', weapon: 'RPG', price: '175k', demand: '5.5/10', availability: 'Limited Time' },
  { id: 72, category: 'Easter Egg', weapon: 'As Val', price: '145k', demand: '4/10', availability: 'Limited Time' },
  { id: 73, category: 'Easter Egg', weapon: 'Aug', price: '135k', demand: '4.5/10', availability: 'Limited Time' },
  { id: 74, category: 'Easter Egg', weapon: 'Barrett', price: '125k', demand: '2.5/10', availability: 'Limited Time' },
  { id: 75, category: 'Easter Egg', weapon: 'M4A1', price: '105k', demand: '4/10', availability: 'Limited Time' },
  { id: 76, category: 'Easter Egg', weapon: 'AK 47', price: '95k', demand: '2.5/10', availability: 'Limited Time' },
  { id: 77, category: 'Easter Egg', weapon: 'Tommy Gun', price: '85k', demand: '3.5/10', availability: 'Limited Time' },
  { id: 78, category: 'Easter Egg', weapon: 'MP7', price: '50k', demand: '2/10', availability: 'Limited Time' },
];

const weapons = ref([...defaultWeapons]);

// Utility functions
const parsePrice = (price) => {
  const numStr = price.replace(/[^0-9.]/g, '');
  const num = parseFloat(numStr);
  if (price.includes('m')) return num * 1000000;
  if (price.includes('k')) return num * 1000;
  return num;
};

const formatPrice = (value) => {
  if (value >= 1000000) {
    const millions = value / 1000000;
    return millions % 1 === 0 ? `${millions}m` : `${millions.toFixed(1)}m`;
  }
  if (value >= 1000) {
    const thousands = value / 1000;
    return thousands % 1 === 0 ? `${thousands}k` : `${thousands.toFixed(1)}k`;
  }
  return value.toString();
};

const categories = computed(() => {
  return [...new Set(weapons.value.map(w => w.category))].sort();
});

const filteredWeapons = computed(() => {
  let filtered = weapons.value;
  
  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase();
    filtered = filtered.filter(w => 
      w.weapon.toLowerCase().includes(query) ||
      w.category.toLowerCase().includes(query)
    );
  }
  
  if (selectedCategory.value) {
    filtered = filtered.filter(w => w.category === selectedCategory.value);
  }
  
  if (selectedAvailability.value) {
    filtered = filtered.filter(w => w.availability === selectedAvailability.value);
  }
  
  return filtered.sort((a, b) => parsePrice(b.price) - parsePrice(a.price));
});

const leftTotal = computed(() => {
  return leftSide.value.reduce((total, item) => total + parsePrice(item.price), 0);
});

const rightTotal = computed(() => {
  return rightSide.value.reduce((total, item) => total + parsePrice(item.price), 0);
});

const tradeDifference = computed(() => {
  return leftTotal.value - rightTotal.value;
});

const tradeResult = computed(() => {
  const diff = Math.abs(tradeDifference.value);
  const percentage = diff / Math.max(leftTotal.value, rightTotal.value) * 100;
  
  if (tradeDifference.value === 0) {
    return { type: 'fair', message: '✅ Fair Trade!', class: 'text-green-400' };
  } else if (percentage <= 10) {
    return { type: 'close', message: '⚖️ Close Trade', class: 'text-yellow-400' };
  } else if (tradeDifference.value > 0) {
    return { type: 'left-wins', message: '📈 Left Side Wins', class: 'text-blue-400' };
  } else {
    return { type: 'right-wins', message: '📈 Right Side Wins', class: 'text-purple-400' };
  }
});

// Functions
const openAddModal = (side) => {
  currentSide.value = side;
  showAddModal.value = true;
  searchQuery.value = '';
  selectedCategory.value = '';
  selectedAvailability.value = '';
};

const addWeapon = (weapon) => {
  const weaponCopy = { ...weapon, uniqueId: Date.now() + Math.random() };
  
  if (currentSide.value === 'left') {
    leftSide.value.push(weaponCopy);
  } else {
    rightSide.value.push(weaponCopy);
  }
  
  showAddModal.value = false;
};

const removeWeapon = (side, uniqueId) => {
  if (side === 'left') {
    leftSide.value = leftSide.value.filter(w => w.uniqueId !== uniqueId);
  } else {
    rightSide.value = rightSide.value.filter(w => w.uniqueId !== uniqueId);
  }
};

const clearSide = (side) => {
  if (side === 'left') {
    leftSide.value = [];
  } else {
    rightSide.value = [];
  }
};

const clearAll = () => {
  leftSide.value = [];
  rightSide.value = [];
};

const getRarityColor = (category) => {
  const colors = {
    'Invisible': 'bg-purple-900 text-purple-200',
    'Obsidian': 'bg-gray-800 text-gray-200',
    'Void': 'bg-black text-purple-300 border border-purple-500',
    'Cyberpunk': 'bg-cyan-900 text-cyan-200',
    'Solid Gold': 'bg-yellow-600 text-yellow-100',
    'Frozen Diamond': 'bg-blue-900 text-blue-200',
    'Amethyst': 'bg-purple-700 text-purple-100',
    'Dark Matter': 'bg-red-900 text-red-200',
    'Anti Matter': 'bg-gray-700 text-gray-200',
    'Crimson Blood': 'bg-red-800 text-red-200',
    'Tactical': 'bg-green-800 text-green-200',
    'Easter Egg': 'bg-green-700 text-green-200'
  };
  return colors[category] || 'bg-gray-600 text-gray-200';
};

const getAvailabilityColor = (availability) => {
  const colors = {
    'Obtainable': 'bg-green-700 text-green-200',
    'Unobtainable': 'bg-red-700 text-red-200',
    'Limited Time': 'bg-yellow-700 text-yellow-200'
  };
  return colors[availability] || 'bg-gray-600 text-gray-200';
};
</script>

<template>
  <!-- Navigation -->
   <nav class="fixed top-0 left-0 w-full backdrop-blur-md bg-black/50 border-b border-purple-800 shadow-lg flex justify-between items-center px-6 py-4 z-40">
    <button @click="emit('switch-to-home')" class="text-2xl font-bold text-purple-300 hover:text-white transition">Stack's Ohio Values</button>
    <!-- Desktop Menu -->
    <div class="hidden lg:flex space-x-6">
      <button @click="emit('switch-to-home')" class="text-purple-300 hover:text-white transition">Home</button>
      <a href="#" class="text-white font-semibold">Calculator</a>
      <a href="#" class="text-purple-300 hover:text-white transition">About</a>
    </div>
    <!-- Mobile Hamburger Icon -->
    <button @click="isMenuOpen = !isMenuOpen" class="lg:hidden text-white hover:text-purple-400 transition">
      <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" class="w-8 h-8">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7" />
      </svg>
    </button>
    <!-- Mobile Dropdown Menu -->
    <div v-if="isMenuOpen" class="absolute top-16 right-6 bg-black/50 backdrop-blur-lg border border-purple-700 shadow-xl rounded-xl p-4 space-y-3 w-48 lg:hidden">
      <button @click="emit('switch-to-home'); isMenuOpen = false" class="block w-full text-left px-4 py-2 text-purple-300 hover:bg-purple-700 hover:text-white rounded transition">Home</button>
      <a href="#" class="block px-4 py-2 text-white bg-purple-700 rounded">Calculator</a>
      <a href="#" class="block px-4 py-2 text-purple-300 hover:bg-purple-700 hover:text-white rounded transition">About</a>
    </div>
  </nav>
  <!-- Main Content -->
  <div class="pt-20 min-h-screen bg-gradient-to-b from-gray-900 via-purple-900/20 to-black">
    <div class="container mx-auto px-6 py-8">
      <!-- Header -->
      <div class="text-center mb-8">
        <h1 class="text-4xl font-bold text-purple-300 mb-4">⚖️ Trade Calculator</h1>
        <p class="text-purple-200">Compare weapon skin values to check if your trade is fair</p>
      </div>

      <!-- Trade Result -->
      <div class="mb-8 text-center">
        <div class="bg-gray-900/50 border border-purple-700 rounded-xl p-6">
          <div class="text-3xl font-bold mb-2" :class="tradeResult.class">
            {{ tradeResult.message }}
          </div>
          <div v-if="tradeDifference !== 0" class="text-purple-300">
            Difference: {{ formatPrice(Math.abs(tradeDifference)) }}
            <span v-if="tradeDifference > 0">(Left side is worth more)</span>
            <span v-else>(Right side is worth more)</span>
          </div>
          <div class="mt-4">
            <button @click="clearAll" class="bg-red-600 hover:bg-red-700 px-4 py-2 rounded-lg transition text-white font-semibold">
              🗑️ Clear All
            </button>
          </div>
        </div>
      </div>

      <!-- Trade Comparison -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-8 mb-8">
        <!-- Left Side -->
        <div class="bg-gray-900/50 border-2 border-blue-600 rounded-xl p-6">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold text-blue-400">📦 Your Offer</h2>
            <div class="flex gap-2">
              <button @click="clearSide('left')" class="text-red-400 hover:text-red-300 text-sm">Clear</button>
              <button @click="openAddModal('left')" class="bg-blue-600 hover:bg-blue-700 px-4 py-2 rounded-lg transition text-white font-semibold">
                ➕ Add Item
              </button>
            </div>
          </div>
          
          <!-- Left Side Items -->
          <div class="space-y-3 mb-4 min-h-[200px]">
            <div v-if="leftSide.length === 0" class="text-center text-purple-400 py-8">
              <div class="text-4xl mb-2">📭</div>
              <p>Click "Add Item" to start building your offer</p>
            </div>
            
            <div v-for="weapon in leftSide" :key="weapon.uniqueId" 
                 class="bg-gray-800/50 border border-purple-600 rounded-lg p-4 flex justify-between items-center">
              <div class="flex items-center space-x-3">
                <span class="px-2 py-1 rounded text-xs font-semibold" :class="getRarityColor(weapon.category)">
                  {{ weapon.category }}
                </span>
                <div>
                  <div class="font-semibold text-white">{{ weapon.weapon }}</div>
                  <div class="text-green-400 font-bold">💵 {{ weapon.price }}</div>
                </div>
              </div>
              <button @click="removeWeapon('left', weapon.uniqueId)" 
                      class="text-red-400 hover:text-red-300 p-2">
                ❌
              </button>
            </div>
          </div>
          
          <!-- Left Side Total -->
          <div class="border-t border-purple-600 pt-4">
            <div class="text-xl font-bold text-blue-400">
              Total: {{ formatPrice(leftTotal) }}
            </div>
          </div>
        </div>

        <!-- Right Side -->
        <div class="bg-gray-900/50 border-2 border-purple-600 rounded-xl p-6">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold text-purple-400">🎁 Their Offer</h2>
            <div class="flex gap-2">
              <button @click="clearSide('right')" class="text-red-400 hover:text-red-300 text-sm">Clear</button>
              <button @click="openAddModal('right')" class="bg-purple-600 hover:bg-purple-700 px-4 py-2 rounded-lg transition text-white font-semibold">
                ➕ Add Item
              </button>
            </div>
          </div>
          
          <!-- Right Side Items -->
          <div class="space-y-3 mb-4 min-h-[200px]">
            <div v-if="rightSide.length === 0" class="text-center text-purple-400 py-8">
              <div class="text-4xl mb-2">📭</div>
              <p>Click "Add Item" to add their offer</p>
            </div>
            
            <div v-for="weapon in rightSide" :key="weapon.uniqueId" 
                 class="bg-gray-800/50 border border-purple-600 rounded-lg p-4 flex justify-between items-center">
              <div class="flex items-center space-x-3">
                <span class="px-2 py-1 rounded text-xs font-semibold" :class="getRarityColor(weapon.category)">
                  {{ weapon.category }}
                </span>
                <div>
                  <div class="font-semibold text-white">{{ weapon.weapon }}</div>
                  <div class="text-green-400 font-bold">💵 {{ weapon.price }}</div>
                </div>
              </div>
              <button @click="removeWeapon('right', weapon.uniqueId)" 
                      class="text-red-400 hover:text-red-300 p-2">
                ❌
              </button>
            </div>
          </div>
          
          <!-- Right Side Total -->
          <div class="border-t border-purple-600 pt-4">
            <div class="text-xl font-bold text-purple-400">
              Total: {{ formatPrice(rightTotal) }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- Add Item Modal -->
  <div v-if="showAddModal" class="fixed inset-0 z-50 flex items-center justify-center bg-black/70 backdrop-blur-sm">
    <div class="bg-gray-900 border border-purple-600 rounded-xl p-6 max-w-4xl w-full mx-4 max-h-[80vh] overflow-y-auto">
      <div class="flex justify-between items-start mb-6">
        <h3 class="text-2xl font-bold text-purple-300">
          ➕ Add Item to {{ currentSide === 'left' ? 'Your Offer' : 'Their Offer' }}
        </h3>
        <button @click="showAddModal = false" class="text-purple-400 hover:text-white text-2xl">&times;</button>
      </div>

      <!-- Search and Filters -->
      <div class="mb-6 flex flex-col lg:flex-row gap-4">
        <div class="flex-1">
          <input 
            v-model="searchQuery" 
            type="text" 
            placeholder="Search for weapon or skin..." 
            class="w-full px-4 py-3 bg-gray-800 border border-purple-600 rounded-lg text-white placeholder-purple-400 focus:border-purple-400 focus:outline-none"
          >
        </div>
        
        <div class="flex flex-col sm:flex-row gap-4">
          <select v-model="selectedCategory" class="px-4 py-3 bg-gray-800 border border-purple-600 rounded-lg text-white focus:border-purple-400 focus:outline-none">
            <option value="">All Categories</option>
            <option v-for="category in categories" :key="category" :value="category">{{ category }}</option>
          </select>
          
          <select v-model="selectedAvailability" class="px-4 py-3 bg-gray-800 border border-purple-600 rounded-lg text-white focus:border-purple-400 focus:outline-none">
            <option value="">All Availability</option>
            <option value="Obtainable">Obtainable</option>
            <option value="Unobtainable">Unobtainable</option>
            <option value="Limited Time">Limited Time</option>
          </select>
        </div>
      </div>

      <!-- Results Count -->
      <div class="mb-4 text-purple-300">
        Showing {{ filteredWeapons.length }} weapons
      </div>

      <!-- Weapons List -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 max-h-[400px] overflow-y-auto">
        <div v-for="weapon in filteredWeapons" :key="weapon.id" 
             @click="addWeapon(weapon)"
             class="bg-gray-800/70 border border-purple-700 rounded-lg p-4 hover:border-purple-500 hover:bg-gray-700/70 transition-all cursor-pointer">
          
          <!-- Rarity and Availability -->
          <div class="flex justify-between items-start mb-3">
            <span class="px-2 py-1 rounded-full text-xs font-semibold" :class="getRarityColor(weapon.category)">
              {{ weapon.category }}
            </span>
            <span class="px-2 py-1 rounded text-xs" :class="getAvailabilityColor(weapon.availability)">
              {{ weapon.availability }}
            </span>
          </div>

          <!-- Weapon Info -->
          <div>
            <h4 class="text-lg font-bold text-white mb-2">{{ weapon.weapon }}</h4>
            <div class="flex justify-between items-center">
              <span class="text-xl font-bold text-green-400">💵 {{ weapon.price }}</span>
              <div class="flex items-center">
                <span class="text-yellow-400 mr-1">⭐</span>
                <span class="font-semibold text-white text-sm">{{ weapon.demand }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div v-if="filteredWeapons.length === 0" class="text-center py-12">
        <div class="text-6xl mb-4">😔</div>
        <h3 class="text-2xl font-bold text-purple-300 mb-2">No weapons found</h3>
        <p class="text-purple-400">Try adjusting your search or filters</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
:deep(body) {
  background-color: #0d021a;
  color: white;
}

/* Smooth scrolling */
html {
  scroll-behavior: smooth;
}

/* Custom scrollbar */
::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: #1a1a2e;
}

::-webkit-scrollbar-thumb {
  background: #6b46c1;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #7c3aed;
}

/* Modal scrollbar */
.overflow-y-auto::-webkit-scrollbar {
  width: 6px;
}

.overflow-y-auto::-webkit-scrollbar-track {
  background: #374151;
}

.overflow-y-auto::-webkit-scrollbar-thumb {
  background: #6b46c1;
  border-radius: 3px;
}
</style>
