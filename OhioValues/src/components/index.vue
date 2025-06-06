<script setup>
import { ref, computed, onMounted } from 'vue';

// Navigation
const isMenuOpen = ref(false);

// Disclaimer modal
const showDisclaimer = ref(true);
const dontShowAgain = ref(false);

// Developer mode (set to false for production)
const isDeveloperMode = ref(false); // Change to true for dev access
const showAddForm = ref(false);
const newWeapon = ref({
  category: '',
  weapon: '',
  price: '',
  demand: '',
  availability: ''
});

// Filters and search
const searchQuery = ref('');
const selectedCategory = ref('');
const selectedAvailability = ref('');
const sortBy = ref('price-desc');

// DEVELOPER SECTION - Easy weapon management
// To add new weapons, simply add them to this array following the format:
// { id: uniqueNumber, category: 'SkinType', weapon: 'WeaponName', price: 'PriceValue', demand: 'X/10', availability: 'Status' }

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
  
  // === INSTRUCTIONS FOR DEVELOPERS ===
  // 1. To add a new weapon, create a new object with a unique ID
  // 2. Follow this format: { id: nextAvailableNumber, category: 'CategoryName', weapon: 'WeaponName', price: 'PriceWithSuffix', demand: 'X/10', availability: 'Status' }
  // 3. Common categories: 'Invisible', 'Obsidian', 'Void', 'Cyberpunk', 'Solid Gold', 'Frozen Diamond', 'Tactical', 'Amethyst', 'Dark Matter', 'Anti Matter', 'Crimson Blood', 'Easter Egg'
  // 4. Price format: Use 'm' for millions, 'k' for thousands (e.g., '1.5m', '250k')
  // 5. Demand format: Always use 'X/10' or 'X.X/10' (e.g., '8/10', '7.5/10')
  // 6. Availability options: 'Obtainable', 'Unobtainable', 'Limited Time'
  // 7. Example: { id: 79, category: 'New Category', weapon: 'New Weapon', price: '2.5m', demand: '9/10', availability: 'Unobtainable' }
];

// Weapons data - loads from default weapons array
const weapons = ref([...defaultWeapons]);

// Computed properties
const categories = computed(() => {
  return [...new Set(weapons.value.map(w => w.category))].sort();
});

const filteredWeapons = computed(() => {
  let filtered = weapons.value;
  
  // Search filter
  if (searchQuery.value) {
    const query = searchQuery.value.toLowerCase();
    filtered = filtered.filter(w => 
      w.weapon.toLowerCase().includes(query) ||
      w.category.toLowerCase().includes(query)
    );
  }
  
  // Category filter
  if (selectedCategory.value) {
    filtered = filtered.filter(w => w.category === selectedCategory.value);
  }
  
  // Availability filter
  if (selectedAvailability.value) {
    filtered = filtered.filter(w => w.availability === selectedAvailability.value);
  }
  
  // Sort
  filtered.sort((a, b) => {
    switch (sortBy.value) {
      case 'price-desc':
        return parsePrice(b.price) - parsePrice(a.price);
      case 'price-asc':
        return parsePrice(a.price) - parsePrice(b.price);
      case 'demand-desc':
        return parseDemand(b.demand) - parseDemand(a.demand);
      case 'demand-asc':
        return parseDemand(a.demand) - parseDemand(b.demand);
      case 'name':
        return a.weapon.localeCompare(b.weapon);
      default:
        return 0;
    }
  });
  
  return filtered;
});

// Methods
const closeDisclaimer = () => {
  showDisclaimer.value = false;
  if (dontShowAgain.value) {
    localStorage.setItem('ohioValuesDisclaimerDismissed', 'true');
  }
};

const addWeapon = () => {
  if (!isDeveloperMode.value) return; // Prevent non-dev access
  
  if (newWeapon.value.category && newWeapon.value.weapon && newWeapon.value.price && 
      newWeapon.value.demand && newWeapon.value.availability) {
    const weapon = {
      id: Date.now(),
      ...newWeapon.value
    };
    weapons.value.push(weapon);
    
    // Reset form
    newWeapon.value = {
      category: '',
      weapon: '',
      price: '',
      demand: '',
      availability: ''
    };
    showAddForm.value = false;
    
    // Save to localStorage (for dev testing)
    localStorage.setItem('ohioValuesWeapons', JSON.stringify(weapons.value));
  }
};

const deleteWeapon = (id) => {
  if (!isDeveloperMode.value) return; // Prevent non-dev access
  weapons.value = weapons.value.filter(w => w.id !== id);
  localStorage.setItem('ohioValuesWeapons', JSON.stringify(weapons.value));
};

const parsePrice = (price) => {
  const numStr = price.replace(/[^0-9.]/g, '');
  const num = parseFloat(numStr);
  if (price.includes('m')) return num * 1000000;
  if (price.includes('k')) return num * 1000;
  return num;
};

const parseDemand = (demand) => {
  // Handle special cases like "+10/10" and extract the numeric value
  const cleanDemand = demand.replace(/[^0-9.]/g, '');
  const num = parseFloat(cleanDemand);
  return isNaN(num) ? 0 : num;
};

const getDemandPercentage = (demand) => {
  const num = parseDemand(demand);
  // Handle special cases like "+10/10" and ensure max is 100%
  const percentage = Math.min((num / 10) * 100, 100);
  return Math.max(percentage, 0); // Ensure no negative values
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

// Load data from localStorage on mount
onMounted(() => {
  const dismissed = localStorage.getItem('ohioValuesDisclaimerDismissed');
  if (dismissed === 'true') {
    showDisclaimer.value = false;
  }
  
  const savedWeapons = localStorage.getItem('ohioValuesWeapons');
  if (savedWeapons) {
    weapons.value = JSON.parse(savedWeapons);
  }
});
</script>

<template>
  <!-- Disclaimer Modal -->
  <div v-if="showDisclaimer" class="fixed inset-0 z-50 flex items-center justify-center bg-black/70 backdrop-blur-sm">
    <div class="bg-gray-900 border border-purple-600 rounded-xl p-8 max-w-md mx-4 shadow-2xl">
      <div class="flex justify-between items-start mb-4">
        <h3 class="text-xl font-bold text-purple-300">⚠️ Disclaimer</h3>
        <button @click="closeDisclaimer" class="text-purple-400 hover:text-white text-2xl">&times;</button>
      </div>
      <p class="text-purple-200 mb-6">
        These weapon skin values are determined by me personally and may not be 100% accurate. 
        Please use them as a general reference only and don't judge trades solely based on these values.
      </p>
      <div class="flex items-center justify-between">
        <label class="flex items-center text-purple-300">
          <input v-model="dontShowAgain" type="checkbox" class="mr-2 accent-purple-600">
          Don't show again
        </label>
        <button @click="closeDisclaimer" class="bg-purple-600 hover:bg-purple-700 px-4 py-2 rounded transition">
          Got it!
        </button>
      </div>
    </div>
  </div>

  <!-- Navigation -->
  <nav class="fixed top-0 left-0 w-full backdrop-blur-md bg-black/50 border-b border-purple-800 shadow-lg flex justify-between items-center px-6 py-4 z-40">
    <a class="text-2xl font-bold text-purple-300">Ohio Values</a>
    <!-- Desktop Menu -->
    <div class="hidden lg:flex space-x-6">
      <a href="#" class="text-purple-300 hover:text-white transition">Home</a>
      <a href="#" class="text-purple-300 hover:text-white transition">Values</a>
      <a href="#" class="text-purple-300 hover:text-white transition">Forum</a>
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
      <a href="#" class="block px-4 py-2 text-purple-300 hover:bg-purple-700 hover:text-white rounded transition">Home</a>
      <a href="#" class="block px-4 py-2 text-purple-300 hover:bg-purple-700 hover:text-white rounded transition">Values</a>
      <a href="#" class="block px-4 py-2 text-purple-300 hover:bg-purple-700 hover:text-white rounded transition">Forum</a>
      <a href="#" class="block px-4 py-2 text-purple-300 hover:bg-purple-700 hover:text-white rounded transition">About</a>
    </div>
  </nav>

  <!-- Main Content -->
  <div class="pt-20 min-h-screen bg-gradient-to-b from-gray-900 via-purple-900/20 to-black">
    <div class="container mx-auto px-6 py-8">
      <!-- Header -->
      <div class="text-center mb-8">
        <h1 class="text-4xl font-bold text-purple-300 mb-4">🔫 Weapon Skin Values</h1>
        <p class="text-purple-200">Browse and search through all weapon skin values</p>
      </div>

      <!-- Controls -->
      <div class="mb-8 flex flex-col lg:flex-row gap-4 bg-gray-900/50 p-6 rounded-xl border border-purple-800">
        <!-- Search -->
        <div class="flex-1">
          <input 
            v-model="searchQuery" 
            type="text" 
            placeholder="Search for weapon or skin..." 
            class="w-full px-4 py-3 bg-gray-800 border border-purple-600 rounded-lg text-white placeholder-purple-400 focus:border-purple-400 focus:outline-none"
          >
        </div>
        
        <!-- Filters -->
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
          
          <select v-model="sortBy" class="px-4 py-3 bg-gray-800 border border-purple-600 rounded-lg text-white focus:border-purple-400 focus:outline-none">
            <option value="price-desc">Price (High to Low)</option>
            <option value="price-asc">Price (Low to High)</option>
            <option value="demand-desc">Demand (High to Low)</option>
            <option value="demand-asc">Demand (Low to High)</option>
            <option value="name">Name (A-Z)</option>
          </select>
        </div>
      </div>

      <!-- Developer Controls (only show if dev mode enabled) -->
      <div v-if="isDeveloperMode" class="mb-6">
        <div class="bg-red-900/20 border border-red-600 rounded-lg p-4 mb-4">
          <h3 class="text-red-400 font-bold mb-2">🔧 Developer Mode Active</h3>
          <p class="text-red-300 text-sm">You can add and delete weapons. Set isDeveloperMode to false for production.</p>
        </div>
        
        <div class="flex justify-end">
          <button @click="showAddForm = !showAddForm" class="bg-purple-600 hover:bg-purple-700 px-6 py-3 rounded-lg transition font-semibold">
            {{ showAddForm ? 'Cancel' : '+ Add New Weapon' }}
          </button>
        </div>
      </div>

      <!-- Add New Weapon Form -->
      <div v-if="showAddForm" class="mb-8 bg-gray-900/70 p-6 rounded-xl border border-purple-700">
        <h3 class="text-xl font-bold text-purple-300 mb-4">Add New Weapon Skin</h3>
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
          <input v-model="newWeapon.category" placeholder="Category" class="px-4 py-2 bg-gray-800 border border-purple-600 rounded text-white placeholder-purple-400">
          <input v-model="newWeapon.weapon" placeholder="Weapon Name" class="px-4 py-2 bg-gray-800 border border-purple-600 rounded text-white placeholder-purple-400">
          <input v-model="newWeapon.price" placeholder="Price (e.g., 1.5m)" class="px-4 py-2 bg-gray-800 border border-purple-600 rounded text-white placeholder-purple-400">
          <input v-model="newWeapon.demand" placeholder="Demand (e.g., 8/10)" class="px-4 py-2 bg-gray-800 border border-purple-600 rounded text-white placeholder-purple-400">
          <select v-model="newWeapon.availability" class="px-4 py-2 bg-gray-800 border border-purple-600 rounded text-white">
            <option value="">Select Availability</option>
            <option value="Obtainable">Obtainable</option>
            <option value="Unobtainable">Unobtainable</option>
            <option value="Limited Time">Limited Time</option>
          </select>
          <button @click="addWeapon" class="bg-green-600 hover:bg-green-700 px-4 py-2 rounded transition font-semibold">
            Add Weapon
          </button>
        </div>
      </div>

      <!-- Results Count -->
      <div class="mb-4 text-purple-300">
        Showing {{ filteredWeapons.length }} of {{ weapons.length }} weapons
      </div>

      <!-- Weapons Grid -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
        <div 
          v-for="weapon in filteredWeapons" 
          :key="weapon.id" 
          class="bg-gray-900/70 border border-purple-700 rounded-xl p-6 hover:border-purple-500 transition-all duration-300 hover:-translate-y-1 hover:shadow-lg hover:shadow-purple-500/20"
        >
          <!-- Rarity Badge -->
          <div class="flex justify-between items-start mb-4">
            <span 
              class="px-3 py-1 rounded-full text-xs font-semibold"
              :class="getRarityColor(weapon.category)"
            >
              {{ weapon.category }}
            </span>
            <span 
              class="px-2 py-1 rounded text-xs"
              :class="getAvailabilityColor(weapon.availability)"
            >
              {{ weapon.availability }}
            </span>
          </div>

          <!-- Weapon Info -->
          <div class="mb-4">
            <h3 class="text-xl font-bold text-white mb-2">{{ weapon.weapon }}</h3>
            <div class="flex justify-between items-center">
              <span class="text-2xl font-bold text-green-400">💵 {{ weapon.price }}</span>
              <div class="flex items-center">
                <span class="text-purple-300 mr-2">Demand:</span>
                <div class="flex items-center">
                  <span class="text-yellow-400 mr-1">⭐</span>
                  <span class="font-semibold text-white">{{ weapon.demand }}</span>
                </div>
              </div>
            </div>
          </div>

          <!-- Demand Bar -->
          <div class="w-full bg-gray-700 rounded-full h-3 mb-3 overflow-hidden">
            <div 
              class="bg-gradient-to-r from-purple-500 to-pink-500 h-full rounded-full transition-all duration-500 ease-out"
              :style="{ 
                width: getDemandPercentage(weapon.demand) + '%',
                minWidth: getDemandPercentage(weapon.demand) > 0 ? '2px' : '0px'
              }"
            ></div>
          </div>

          <!-- Delete Button -->
          <button 
            @click="deleteWeapon(weapon.id)" 
            class="w-full mt-2 bg-red-600/20 hover:bg-red-600 text-red-400 hover:text-white px-3 py-2 rounded transition text-sm"
          >
            🗑️ Delete
          </button>
        </div>
      </div>

      <!-- No Results -->
      <div v-if="filteredWeapons.length === 0" class="text-center py-12">
        <div class="text-6xl mb-4">😔</div>
        <h3 class="text-2xl font-bold text-purple-300 mb-2">No weapons found</h3>
        <p class="text-purple-400">Try adjusting your search or filters</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Apply a translucent dark theme globally */
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
</style>
