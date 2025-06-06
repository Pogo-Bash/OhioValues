<script setup>
import { ref, computed, onMounted } from 'vue';

const isMenuOpen = ref(false);
const showDisclaimer = ref(true);
const dontShowAgain = ref(false);
const isDeveloperMode = ref(false); 
const showAddForm = ref(false); // This was missing!

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

// Your weapons data array stays the same...
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

const closeDisclaimer = () => {
  showDisclaimer.value = false;
  if (dontShowAgain.value) {
    localStorage.setItem('ohioValuesDisclaimerDismissed', 'true');
  }
};

const addWeapon = () => {
  if (!isDeveloperMode.value) return; 
  
  if (newWeapon.value.category && newWeapon.value.weapon && newWeapon.value.price && 
      newWeapon.value.demand && newWeapon.value.availability) {
    const weapon = {
      id: Date.now(),
      ...newWeapon.value
    };
    weapons.value.push(weapon);
    
    newWeapon.value = {
      category: '',
      weapon: '',
      price: '',
      demand: '',
      availability: ''
    };
    showAddForm.value = false;
    
    localStorage.setItem('ohioValuesWeapons', JSON.stringify(weapons.value));
  }
};

const deleteWeapon = (id) => {
  if (!isDeveloperMode.value) return; 
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
  const cleanDemand = demand.replace(/[^0-9.]/g, '');
  const num = parseFloat(cleanDemand);
  return isNaN(num) ? 0 : num;
};

// FIXED DEMAND PERCENTAGE CALCULATION
const getDemandPercentage = (demand, category, price) => {
  const demandNum = parseDemand(demand);
  const priceValue = parsePrice(price);
  
  // Calculate demand component (1 demand = 1/10 of the bar)
  const demandComponent = (demandNum / 10) * 100;
  
  // Calculate value component (2m value = 1/10 of the bar, so 20m = 100%)
  const valueComponent = (priceValue / 20000000) * 100;
  
  // Average the two components
  const averagePercentage = (demandComponent + valueComponent) / 2;
  
  // Ensure the result is between 0 and 100
  return Math.min(Math.max(averagePercentage, 0), 100);
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

const getDemandBarColor = (category, demand) => {
  const demandNum = parseDemand(demand);
  
  // Ultra-rare categories get special colors
  const ultraRareCategories = ['Invisible', 'Obsidian', 'Void', 'Cyberpunk'];
  const highTierCategories = ['Solid Gold', 'Frozen Diamond', 'Tactical', 'Amethyst'];
  
  if (ultraRareCategories.includes(category)) {
    if (demandNum >= 8) return 'bg-gradient-to-r from-purple-400 via-pink-400 to-red-400'; // Legendary
    if (demandNum >= 6) return 'bg-gradient-to-r from-purple-500 to-pink-500'; // Epic
    return 'bg-gradient-to-r from-purple-600 to-purple-400'; // Rare ultra
  }
  
  if (highTierCategories.includes(category)) {
    if (demandNum >= 8) return 'bg-gradient-to-r from-blue-400 via-purple-400 to-pink-400'; // High demand
    if (demandNum >= 6) return 'bg-gradient-to-r from-blue-500 to-purple-500'; // Good demand
    return 'bg-gradient-to-r from-blue-600 to-blue-400'; // Standard high tier
  }
  
  // Standard/obtainable categories
  if (demandNum >= 8) return 'bg-gradient-to-r from-yellow-400 to-green-400'; // High demand standard
  if (demandNum >= 6) return 'bg-gradient-to-r from-yellow-500 to-green-500'; // Good demand
  if (demandNum >= 4) return 'bg-gradient-to-r from-orange-500 to-yellow-500'; // Medium demand
  return 'bg-gradient-to-r from-red-500 to-orange-500'; // Low demand
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
