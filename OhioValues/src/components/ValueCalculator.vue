<script setup>
import { ref, computed, onMounted } from 'vue';

// Add props to receive navigation function
const props = defineProps({
  showHome: Function
});

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
  { id: 11, category: 'Invisible', weapon: 'Minigun', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 12, category: 'Invisible', weapon: 'FNFAL', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 13, category: 'Invisible', weapon: 'AK-47', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 14, category: 'Invisible', weapon: 'M249 SAW', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 15, category: 'Invisible', weapon: 'M4A1', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 16, category: 'Invisible', weapon: 'Tommy Gun', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 17, category: 'Invisible', weapon: 'Double Barrel', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 18, category: 'Invisible', weapon: 'RPK', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 19, category: 'Invisible', weapon: 'Deagle', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 20, category: 'Invisible', weapon: 'Raygun', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 21, category: 'Invisible', weapon: 'AR-15', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 22, category: 'Invisible', weapon: 'M1911', price: '?', demand: '?', availability: 'Unobtainable' },
  { id: 23, category: 'Invisible', weapon: 'USP', price: '?', demand: '?', availability: 'Unobtainable' },
  
  // Obsidian skins - Very rare
  { id: 24, category: 'Obsidian', weapon: 'Minigun', price: '15m', demand: '6/10', availability: 'Unobtainable' },
  { id: 25, category: 'Obsidian', weapon: 'FNFAL', price: '12.5m', demand: '8/10', availability: 'Unobtainable' },
  { id: 26, category: 'Obsidian', weapon: 'RPG', price: '12m', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 27, category: 'Obsidian', weapon: 'AS Val', price: '10m', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 28, category: 'Obsidian', weapon: 'Aug', price: '9m', demand: '7/10', availability: 'Unobtainable' },
  { id: 29, category: 'Obsidian', weapon: 'Barrett', price: '9m', demand: '4/10', availability: 'Unobtainable' },
  { id: 30, category: 'Obsidian', weapon: 'P90', price: '9m', demand: '7/10', availability: 'Unobtainable' },
  { id: 31, category: 'Obsidian', weapon: 'Scar', price: '7m', demand: '7/10', availability: 'Unobtainable' },
  { id: 32, category: 'Obsidian', weapon: 'AK-47', price: '6m', demand: '5/10', availability: 'Unobtainable' },
  { id: 33, category: 'Obsidian', weapon: 'M1', price: '6m', demand: '6/10', availability: 'Unobtainable' },
  { id: 34, category: 'Obsidian', weapon: 'M249 SAW', price: '6m', demand: '1/10', availability: 'Unobtainable' },
  { id: 35, category: 'Obsidian', weapon: 'M4A1', price: '6m', demand: '6/10', availability: 'Unobtainable' },
  { id: 36, category: 'Obsidian', weapon: 'Tommy Gun', price: '6m', demand: '6/10', availability: 'Unobtainable' },
  { id: 37, category: 'Obsidian', weapon: 'Double Barrel', price: '5m', demand: '4.5/10', availability: 'Unobtainable' },
  { id: 38, category: 'Obsidian', weapon: 'Riot Shield', price: '5m', demand: '3.5/10', availability: 'Unobtainable' },
  { id: 39, category: 'Obsidian', weapon: 'RPK', price: '5m', demand: '3/10', availability: 'Unobtainable' },
  { id: 40, category: 'Obsidian', weapon: 'Deagle', price: '4m', demand: '3/10', availability: 'Unobtainable' },
  { id: 41, category: 'Obsidian', weapon: 'Flamethrower', price: '4m', demand: '2/10', availability: 'Unobtainable' },
  { id: 42, category: 'Obsidian', weapon: 'Raygun', price: '4m', demand: '2/10', availability: 'Unobtainable' },
  { id: 43, category: 'Obsidian', weapon: 'AR-15', price: '3m', demand: '2/10', availability: 'Unobtainable' },
  { id: 44, category: 'Obsidian', weapon: 'Python', price: '3m', demand: '3/10', availability: 'Unobtainable' },
  { id: 45, category: 'Obsidian', weapon: 'M1911', price: '2.5m', demand: '1/10', availability: 'Unobtainable' },
  { id: 46, category: 'Obsidian', weapon: 'USP', price: '2m', demand: '1/10', availability: 'Unobtainable' },
  
  // Void skins - High tier
  { id: 47, category: 'Void', weapon: 'RPG', price: '8.5m', demand: '10/10', availability: 'Unobtainable' },
  { id: 48, category: 'Void', weapon: 'AS VAL', price: '6.8m', demand: '9/10', availability: 'Unobtainable' },
  { id: 49, category: 'Void', weapon: 'AUG', price: '6.5m', demand: '9/10', availability: 'Unobtainable' },
  { id: 50, category: 'Void', weapon: 'M4A1', price: '5.4m', demand: '7/10', availability: 'Unobtainable' },
  { id: 51, category: 'Void', weapon: 'Barrett', price: '5.2m', demand: '7/10', availability: 'Unobtainable' },
  { id: 52, category: 'Void', weapon: 'AK-47', price: '4m', demand: '7/10', availability: 'Unobtainable' },
  { id: 53, category: 'Void', weapon: 'Tommy Gun', price: '3.3m', demand: '7/10', availability: 'Unobtainable' },
  { id: 54, category: 'Void', weapon: 'Riot Shield', price: '2.5m', demand: '7/10', availability: 'Unobtainable' },
  { id: 55, category: 'Void', weapon: 'MP7', price: '2.4m', demand: '6/10', availability: 'Unobtainable' },
  { id: 56, category: 'Void', weapon: 'Sawn Off', price: '2.3m', demand: '3/10', availability: 'Unobtainable' },
  { id: 57, category: 'Void', weapon: 'RPK', price: '2.2m', demand: '5/10', availability: 'Unobtainable' },
  { id: 58, category: 'Void', weapon: 'M249 SAW', price: '2.1m', demand: '4/10', availability: 'Unobtainable' },
  { id: 59, category: 'Void', weapon: 'Double Barrel', price: '1.9m', demand: '3/10', availability: 'Unobtainable' },
  { id: 60, category: 'Void', weapon: 'Raygun', price: '1.5m', demand: '4/10', availability: 'Unobtainable' },
  { id: 61, category: 'Void', weapon: 'Deagle', price: '1.3m', demand: '2/10', availability: 'Unobtainable' },
  { id: 62, category: 'Void', weapon: 'Mossberg', price: '1.3m', demand: '1/10', availability: 'Unobtainable' },
  { id: 63, category: 'Void', weapon: 'AR-15', price: '1.2m', demand: '1/10', availability: 'Unobtainable' },
  { id: 64, category: 'Void', weapon: 'Flamethrower', price: '1.1m', demand: '2/10', availability: 'Unobtainable' },
  { id: 65, category: 'Void', weapon: 'Python', price: '1.1m', demand: '1/10', availability: 'Unobtainable' },
  { id: 66, category: 'Void', weapon: 'Uzi', price: '1m', demand: '3/10', availability: 'Unobtainable' },
  { id: 67, category: 'Void', weapon: 'Saiga-12', price: '850k', demand: '1/10', availability: 'Unobtainable' },
  { id: 68, category: 'Void', weapon: 'Stagecoach', price: '800k', demand: '1/10', availability: 'Unobtainable' },
  { id: 69, category: 'Void', weapon: 'Dragunov', price: '800k', demand: '1/10', availability: 'Unobtainable' },
  { id: 70, category: 'Void', weapon: 'Glock-18', price: '750k', demand: '1/10', availability: 'Unobtainable' },
  { id: 71, category: 'Void', weapon: 'Glock', price: '600k', demand: '1/10', availability: 'Unobtainable' },
  { id: 72, category: 'Void', weapon: 'M1911', price: '475k', demand: '1/10', availability: 'Unobtainable' },
  { id: 73, category: 'Void', weapon: 'USP', price: '450k', demand: '1/10', availability: 'Unobtainable' },
  
  // Cyberpunk skins - Popular high tier
  { id: 74, category: 'Cyberpunk', weapon: 'AS VAL', price: '4.7m', demand: '10/10', availability: 'Unobtainable' },
  { id: 75, category: 'Cyberpunk', weapon: 'AUG', price: '4.5m', demand: '10/10', availability: 'Unobtainable' },
  { id: 76, category: 'Cyberpunk', weapon: 'M4A1', price: '3.7m', demand: '8.5/10', availability: 'Unobtainable' },
  { id: 77, category: 'Cyberpunk', weapon: 'AK-47', price: '3m', demand: '7/10', availability: 'Unobtainable' },
  { id: 78, category: 'Cyberpunk', weapon: 'Tommy', price: '2.8m', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 79, category: 'Cyberpunk', weapon: 'Sawn Off', price: '2.6m', demand: '6.5/10', availability: 'Unobtainable' },
  { id: 80, category: 'Cyberpunk', weapon: 'Double Barrel', price: '2.3m', demand: '5.5/10', availability: 'Unobtainable' },
  { id: 81, category: 'Cyberpunk', weapon: 'RPK', price: '2.2m', demand: '6/10', availability: 'Unobtainable' },
  { id: 82, category: 'Cyberpunk', weapon: 'Uzi', price: '1.8m', demand: '3.5/10', availability: 'Unobtainable' },
  { id: 83, category: 'Cyberpunk', weapon: 'Glock 18', price: '1.5m', demand: '1.5/10', availability: 'Unobtainable' },
  { id: 84, category: 'Cyberpunk', weapon: 'Glock', price: '1.2m', demand: '1/10', availability: 'Unobtainable' },
  
  // Solid Gold skins
  { id: 85, category: 'Solid Gold', weapon: 'RPG', price: '3.9m', demand: '9/10', availability: 'Unobtainable' },
  { id: 86, category: 'Solid Gold', weapon: 'AS VAL', price: '3.6m', demand: '7/10', availability: 'Unobtainable' },
  { id: 87, category: 'Solid Gold', weapon: 'AUG', price: '3.4m', demand: '7/10', availability: 'Unobtainable' },
  { id: 88, category: 'Solid Gold', weapon: 'Barrett', price: '3.3m', demand: '6/10', availability: 'Unobtainable' },
  { id: 89, category: 'Solid Gold', weapon: 'M4A1', price: '3.2m', demand: '6/10', availability: 'Unobtainable' },
  { id: 90, category: 'Solid Gold', weapon: 'AK-47', price: '2.5m', demand: '6/10', availability: 'Unobtainable' },
  { id: 91, category: 'Solid Gold', weapon: 'Tommy', price: '2.3m', demand: '6/10', availability: 'Unobtainable' },
  { id: 92, category: 'Solid Gold', weapon: 'Sawn Off', price: '1.6m', demand: '3/10', availability: 'Unobtainable' },
  { id: 93, category: 'Solid Gold', weapon: 'Riot Shield', price: '1.6m', demand: '6/10', availability: 'Unobtainable' },
  { id: 94, category: 'Solid Gold', weapon: 'RPK', price: '1.5m', demand: '5/10', availability: 'Unobtainable' },
  { id: 95, category: 'Solid Gold', weapon: 'M249 SAW', price: '1.4m', demand: '4/10', availability: 'Unobtainable' },
  { id: 96, category: 'Solid Gold', weapon: 'Double Barrel', price: '1.3m', demand: '3/10', availability: 'Unobtainable' },
  { id: 97, category: 'Solid Gold', weapon: 'MP7', price: '1.3m', demand: '6/10', availability: 'Unobtainable' },
  { id: 98, category: 'Solid Gold', weapon: 'Deagle', price: '1.1m', demand: '2/10', availability: 'Unobtainable' },
  { id: 99, category: 'Solid Gold', weapon: 'Raygun', price: '950k', demand: '4/10', availability: 'Unobtainable' },
  { id: 100, category: 'Solid Gold', weapon: 'Flamethrower', price: '900k', demand: '2/10', availability: 'Unobtainable' },
  { id: 101, category: 'Solid Gold', weapon: 'Mossberg', price: '850k', demand: '1/10', availability: 'Unobtainable' },
  { id: 102, category: 'Solid Gold', weapon: 'AR-15', price: '800k', demand: '1/10', availability: 'Unobtainable' },
  { id: 103, category: 'Solid Gold', weapon: 'Uzi', price: '650k', demand: '3/10', availability: 'Unobtainable' },
  { id: 104, category: 'Solid Gold', weapon: 'Dragunov', price: '550k', demand: '1/10', availability: 'Unobtainable' },
  { id: 105, category: 'Solid Gold', weapon: 'Python', price: '500k', demand: '1/10', availability: 'Unobtainable' },
  { id: 106, category: 'Solid Gold', weapon: 'Saiga 12', price: '500k', demand: '1/10', availability: 'Unobtainable' },
  { id: 107, category: 'Solid Gold', weapon: 'Glock-18', price: '450k', demand: '1/10', availability: 'Unobtainable' },
  { id: 108, category: 'Solid Gold', weapon: 'Glock', price: '450k', demand: '1/10', availability: 'Unobtainable' },
  { id: 109, category: 'Solid Gold', weapon: 'Stagecoach', price: '450k', demand: '1/10', availability: 'Unobtainable' },
  { id: 110, category: 'Solid Gold', weapon: 'M1911', price: '420k', demand: '1/10', availability: 'Unobtainable' },
  { id: 111, category: 'Solid Gold', weapon: 'USP', price: '400k', demand: '1/10', availability: 'Unobtainable' },
  
  // Frozen Diamond skins
  { id: 112, category: 'Frozen Diamond', weapon: 'RPG', price: '1.5m', demand: '8/10', availability: 'Unobtainable' },
  { id: 113, category: 'Frozen Diamond', weapon: 'Scar L', price: '1.4m', demand: '7/10', availability: 'Unobtainable' },
  { id: 114, category: 'Frozen Diamond', weapon: 'AUG', price: '1.3m', demand: '6/10', availability: 'Unobtainable' },
  { id: 115, category: 'Frozen Diamond', weapon: 'P90', price: '1m', demand: '6/10', availability: 'Unobtainable' },
  { id: 116, category: 'Frozen Diamond', weapon: 'MP7', price: '900k', demand: '5/10', availability: 'Unobtainable' },
  
  // Tactical skins
  { id: 117, category: 'Tactical', weapon: 'Scar L', price: '1.6m', demand: '10/10', availability: 'Unobtainable' },
  { id: 118, category: 'Tactical', weapon: 'AUG', price: '800k', demand: '9/10', availability: 'Unobtainable' },
  { id: 119, category: 'Tactical', weapon: 'AS VAL', price: '750k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 120, category: 'Tactical', weapon: 'Barrett', price: '600k', demand: '6/10', availability: 'Unobtainable' },
  { id: 121, category: 'Tactical', weapon: 'MP7', price: '530k', demand: '7/10', availability: 'Unobtainable' },
  { id: 122, category: 'Tactical', weapon: 'AK-47', price: '510k', demand: '6/10', availability: 'Unobtainable' },
  { id: 123, category: 'Tactical', weapon: 'M249 SAW', price: '400k', demand: '4/10', availability: 'Unobtainable' },
  { id: 124, category: 'Tactical', weapon: 'Glock 18', price: '260k', demand: '1.5/10', availability: 'Unobtainable' },
  { id: 125, category: 'Tactical', weapon: 'Dragunov', price: '235k', demand: '1/10', availability: 'Unobtainable' },
  { id: 126, category: 'Tactical', weapon: 'Glock', price: '210k', demand: '1/10', availability: 'Unobtainable' },
  
  // Amethyst skins (Atomic)
  { id: 127, category: 'Amethyst', weapon: 'RPG', price: '1.2m', demand: '9.5/10', availability: 'Unobtainable' },
  { id: 128, category: 'Amethyst', weapon: 'AS Val', price: '950k', demand: '8.5/10', availability: 'Unobtainable' },
  { id: 129, category: 'Amethyst', weapon: 'M4A1', price: '850k', demand: '8/10', availability: 'Unobtainable' },
  { id: 130, category: 'Amethyst', weapon: 'Sawn Off', price: '750k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 131, category: 'Amethyst', weapon: 'Double Barrel', price: '700k', demand: '7/10', availability: 'Unobtainable' },
  
  // Nature skins (Atomic)
  { id: 132, category: 'Nature', weapon: 'RPG', price: '1m', demand: '9/10', availability: 'Unobtainable' },
  { id: 133, category: 'Nature', weapon: 'AS Val', price: '900k', demand: '9/10', availability: 'Unobtainable' },
  { id: 134, category: 'Nature', weapon: 'M4A1', price: '800k', demand: '8/10', availability: 'Unobtainable' },
  { id: 135, category: 'Nature', weapon: 'Sawn Off', price: '700k', demand: '8/10', availability: 'Unobtainable' },
  { id: 136, category: 'Nature', weapon: 'Double Barrel', price: '650k', demand: '6/10', availability: 'Unobtainable' },
  
  // Water skins (Atomic)
  { id: 137, category: 'Water', weapon: 'RPG', price: '950k', demand: '8.5/10', availability: 'Unobtainable' },
  { id: 138, category: 'Water', weapon: 'AS Val', price: '850k', demand: '8/10', availability: 'Unobtainable' },
  { id: 139, category: 'Water', weapon: 'M4A1', price: '750k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 140, category: 'Water', weapon: 'Sawn Off', price: '650k', demand: '7/10', availability: 'Unobtainable' },
  { id: 141, category: 'Water', weapon: 'Double Barrel', price: '600k', demand: '6/10', availability: 'Unobtainable' },
  
  // Flame skins (Atomic)
  { id: 142, category: 'Flame', weapon: 'RPG', price: '900k', demand: '6.5/10', availability: 'Unobtainable' },
  { id: 143, category: 'Flame', weapon: 'AS Val', price: '750k', demand: '6/10', availability: 'Unobtainable' },
  { id: 144, category: 'Flame', weapon: 'M4A1', price: '650k', demand: '5/10', availability: 'Unobtainable' },
  { id: 145, category: 'Flame', weapon: 'Sawn Off', price: '600k', demand: '4/10', availability: 'Unobtainable' },
  { id: 146, category: 'Flame', weapon: 'Double Barrel', price: '550k', demand: '5/10', availability: 'Unobtainable' },
  
  // Future White skins
  { id: 147, category: 'Future White', weapon: 'Scar L', price: '800k', demand: '8/10', availability: 'Unobtainable' },
  { id: 148, category: 'Future White', weapon: 'Barrett', price: '650k', demand: '7/10', availability: 'Unobtainable' },
  { id: 149, category: 'Future White', weapon: 'AK-47', price: '500k', demand: '6/10', availability: 'Unobtainable' },
  { id: 150, category: 'Future White', weapon: 'Mossberg', price: '300k', demand: '2.5/10', availability: 'Unobtainable' },
  { id: 151, category: 'Future White', weapon: 'Python', price: '250k', demand: '1.5/10', availability: 'Unobtainable' },
  
  // Future Black skins
  { id: 152, category: 'Future Black', weapon: 'Scar L', price: '750k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 153, category: 'Future Black', weapon: 'Barrett', price: '600k', demand: '6.5/10', availability: 'Unobtainable' },
  { id: 154, category: 'Future Black', weapon: 'AK-47', price: '450k', demand: '5.5/10', availability: 'Unobtainable' },
  { id: 155, category: 'Future Black', weapon: 'Mossberg', price: '250k', demand: '2/10', availability: 'Unobtainable' },
  { id: 156, category: 'Future Black', weapon: 'Python', price: '200k', demand: '1/10', availability: 'Unobtainable' },
  
  // Tactical V3 skins
  { id: 157, category: 'Tactical V3', weapon: 'P90', price: '550k', demand: '7/10', availability: 'Unobtainable' },
  { id: 158, category: 'Tactical V3', weapon: 'RPG', price: '370k', demand: '8/10', availability: 'Unobtainable' },
  { id: 159, category: 'Tactical V3', weapon: 'FN FAL', price: '350k', demand: '8/10', availability: 'Unobtainable' },
  { id: 160, category: 'Tactical V3', weapon: 'AWP', price: '325k', demand: '6.5/10', availability: 'Unobtainable' },
  { id: 161, category: 'Tactical V3', weapon: 'M1 Garand', price: '300k', demand: '6/10', availability: 'Unobtainable' },
  { id: 162, category: 'Tactical V3', weapon: 'RPK', price: '240k', demand: '5/10', availability: 'Unobtainable' },
  { id: 163, category: 'Tactical V3', weapon: 'Deagle', price: '180k', demand: '4.5/10', availability: 'Unobtainable' },
  
  // Reaper skins
  { id: 164, category: 'Reaper', weapon: 'AS Val', price: '350k', demand: '7/10', availability: 'Unobtainable' },
  { id: 165, category: 'Reaper', weapon: 'M4A1', price: '285k', demand: '6/10', availability: 'Unobtainable' },
  { id: 166, category: 'Reaper', weapon: 'Barrett M107', price: '265k', demand: '3.5/10', availability: 'Unobtainable' },
  { id: 167, category: 'Reaper', weapon: 'AK-47', price: '255k', demand: '4.5/10', availability: 'Unobtainable' },
  { id: 168, category: 'Reaper', weapon: 'Glock', price: '145k', demand: '1/10', availability: 'Unobtainable' },
  
  // Steampunk skins
  { id: 169, category: 'Steampunk', weapon: 'Scar L', price: '600k', demand: '8.5/10', availability: 'Unobtainable' },
  { id: 170, category: 'Steampunk', weapon: 'Sawn Off', price: '360k', demand: '7/10', availability: 'Unobtainable' },
  { id: 171, category: 'Steampunk', weapon: 'Tommy Gun', price: '350k', demand: '7/10', availability: 'Unobtainable' },
  { id: 172, category: 'Steampunk', weapon: 'MP7', price: '310k', demand: '6/10', availability: 'Unobtainable' },
  { id: 173, category: 'Steampunk', weapon: 'Double Barrel', price: '260k', demand: '5/10', availability: 'Unobtainable' },
  
  // Subzero skins
  { id: 174, category: 'Subzero', weapon: 'RPG', price: '350k', demand: '8/10', availability: 'Unobtainable' },
  { id: 175, category: 'Subzero', weapon: 'AS Val', price: '300k', demand: '6/10', availability: 'Unobtainable' },
  { id: 176, category: 'Subzero', weapon: 'M4A1', price: '290k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 177, category: 'Subzero', weapon: 'Barrett M107', price: '255k', demand: '4.5/10', availability: 'Unobtainable' },
  { id: 178, category: 'Subzero', weapon: 'Deagle', price: '195k', demand: '5/10', availability: 'Unobtainable' },
  
  // Frozen Gold skins
  { id: 179, category: 'Frozen Gold', weapon: 'RPG', price: '450k', demand: '9/10', availability: 'Unobtainable' },
  { id: 180, category: 'Frozen Gold', weapon: 'AS Val', price: '380k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 181, category: 'Frozen Gold', weapon: 'M4A1', price: '350k', demand: '8.5/10', availability: 'Unobtainable' },
  { id: 182, category: 'Frozen Gold', weapon: 'Barrett M107', price: '320k', demand: '5.5/10', availability: 'Unobtainable' },
  { id: 183, category: 'Frozen Gold', weapon: 'Deagle', price: '250k', demand: '6/10', availability: 'Unobtainable' },
  
  // Elite skins
  { id: 184, category: 'Elite', weapon: 'Scar L', price: '450k', demand: '9/10', availability: 'Unobtainable' },
  { id: 185, category: 'Elite', weapon: 'AUG', price: '370k', demand: '8/10', availability: 'Unobtainable' },
  { id: 186, category: 'Elite', weapon: 'AS Val', price: '350k', demand: '7/10', availability: 'Unobtainable' },
  { id: 187, category: 'Elite', weapon: 'M4A1', price: '320k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 188, category: 'Elite', weapon: 'Barrett', price: '240k', demand: '5/10', availability: 'Unobtainable' },
  
  // Sakura skins
  { id: 189, category: 'Sakura', weapon: 'AS Val', price: '250k', demand: '7/10', availability: 'Unobtainable' },
  { id: 190, category: 'Sakura', weapon: 'Tommy Gun', price: '230k', demand: '6.5/10', availability: 'Unobtainable' },
  { id: 191, category: 'Sakura', weapon: 'Barrett', price: '200k', demand: '5.5/10', availability: 'Unobtainable' },
  { id: 192, category: 'Sakura', weapon: 'Uzi', price: '140k', demand: '2.5/10', availability: 'Unobtainable' },
  { id: 193, category: 'Sakura', weapon: 'Python', price: '135k', demand: '1/10', availability: 'Unobtainable' },
  
  // Death Blossom skins
  { id: 194, category: 'Death Blossom', weapon: 'AS Val', price: '290k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 195, category: 'Death Blossom', weapon: 'Tommy Gun', price: '250k', demand: '7/10', availability: 'Unobtainable' },
  { id: 196, category: 'Death Blossom', weapon: 'Barrett', price: '220k', demand: '6/10', availability: 'Unobtainable' },
  { id: 197, category: 'Death Blossom', weapon: 'Uzi', price: '175k', demand: '3/10', availability: 'Unobtainable' },
  { id: 198, category: 'Death Blossom', weapon: 'Python', price: '145k', demand: '1/10', availability: 'Unobtainable' },
  
  // Wasteland skins
  { id: 199, category: 'Wasteland', weapon: 'AS Val', price: '185k', demand: '5/10', availability: 'Unobtainable' },
  { id: 200, category: 'Wasteland', weapon: 'MP7', price: '165k', demand: '4/10', availability: 'Unobtainable' },
  { id: 201, category: 'Wasteland', weapon: 'Tommy Gun', price: '135k', demand: '4/10', availability: 'Unobtainable' },
  { id: 202, category: 'Wasteland', weapon: 'AK-47', price: '125k', demand: '3/10', availability: 'Unobtainable' },
  { id: 203, category: 'Wasteland', weapon: 'Double Barrel', price: '125k', demand: '3/10', availability: 'Unobtainable' },
  
  // Mystic skins
  { id: 204, category: 'Mystic', weapon: 'AUG', price: '175k', demand: '7/10', availability: 'Unobtainable' },
  { id: 205, category: 'Mystic', weapon: 'AS Val', price: '155k', demand: '7/10', availability: 'Unobtainable' },
  { id: 206, category: 'Mystic', weapon: 'Barrett', price: '140k', demand: '5/10', availability: 'Unobtainable' },
  { id: 207, category: 'Mystic', weapon: 'AK-47', price: '135k', demand: '6/10', availability: 'Unobtainable' },
  { id: 208, category: 'Mystic', weapon: 'M249 SAW', price: '125k', demand: '3.5/10', availability: 'Unobtainable' },
  
  // Void Mystic skins
  { id: 209, category: 'Void Mystic', weapon: 'AUG', price: '245k', demand: '8.5/10', availability: 'Unobtainable' },
  { id: 210, category: 'Void Mystic', weapon: 'AS Val', price: '190k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 211, category: 'Void Mystic', weapon: 'AK-47', price: '170k', demand: '6/10', availability: 'Unobtainable' },
  { id: 212, category: 'Void Mystic', weapon: 'Barrett', price: '165k', demand: '6/10', availability: 'Unobtainable' },
  { id: 213, category: 'Void Mystic', weapon: 'M249 SAW', price: '155k', demand: '5/10', availability: 'Unobtainable' },
  
  // Pirate skins
  { id: 214, category: 'Pirate', weapon: 'Scar L', price: '450k', demand: '9/10', availability: 'Unobtainable' },
  { id: 215, category: 'Pirate', weapon: 'Sawn Off', price: '200k', demand: '5/10', availability: 'Unobtainable' },
  { id: 216, category: 'Pirate', weapon: 'RPK', price: '180k', demand: '2/10', availability: 'Unobtainable' },
  { id: 217, category: 'Pirate', weapon: 'Double Barrel', price: '175k', demand: '2/10', availability: 'Unobtainable' },
  { id: 218, category: 'Pirate', weapon: 'Python', price: '140k', demand: '1/10', availability: 'Unobtainable' },
  
  // Treasure skins
  { id: 219, category: 'Treasure', weapon: 'Scar L', price: '700k', demand: '10/10', availability: 'Unobtainable' },
  { id: 220, category: 'Treasure', weapon: 'RPK', price: '240k', demand: '3.5/10', availability: 'Unobtainable' },
  { id: 221, category: 'Treasure', weapon: 'Sawn Off', price: '220k', demand: '6/10', availability: 'Unobtainable' },
  { id: 222, category: 'Treasure', weapon: 'Double Barrel', price: '210k', demand: '2.5/10', availability: 'Unobtainable' },
  { id: 223, category: 'Treasure', weapon: 'Python', price: '180k', demand: '1.5/10', availability: 'Unobtainable' },
  
  // Cannon RPG skins
  { id: 224, category: 'Cannon', weapon: 'RPG', price: '325k', demand: '6/10', availability: 'Unobtainable' },
  { id: 225, category: 'Gold Cannon', weapon: 'RPG', price: '500k', demand: '7.5/10', availability: 'Unobtainable' },
  
  // Biohazard skins
  { id: 226, category: 'Biohazard', weapon: 'AS Val', price: '190k', demand: '7/10', availability: 'Unobtainable' },
  { id: 227, category: 'Biohazard', weapon: 'AUG', price: '185k', demand: '7/10', availability: 'Unobtainable' },
  { id: 228, category: 'Biohazard', weapon: 'Barrett M107', price: '175k', demand: '6/10', availability: 'Unobtainable' },
  { id: 229, category: 'Biohazard', weapon: 'Tommy Gun', price: '160k', demand: '6/10', availability: 'Unobtainable' },
  { id: 230, category: 'Biohazard', weapon: 'MP7', price: '155k', demand: '5.5/10', availability: 'Unobtainable' },
  
  // Void Nightmare skins
  { id: 231, category: 'Void Nightmare', weapon: 'RPG', price: '250k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 232, category: 'Void Nightmare', weapon: 'Scar L', price: '235k', demand: '8/10', availability: 'Unobtainable' },
  { id: 233, category: 'Void Nightmare', weapon: 'AUG', price: '200k', demand: '6.5/10', availability: 'Unobtainable' },
  { id: 234, category: 'Void Nightmare', weapon: 'P90', price: '190k', demand: '6/10', availability: 'Unobtainable' },
  { id: 235, category: 'Void Nightmare', weapon: 'Barrett M107', price: '170k', demand: '4/10', availability: 'Unobtainable' },
  
  // WW2 skins
  { id: 236, category: 'WW2', weapon: 'P90', price: '260k', demand: '8/10', availability: 'Unobtainable' },
  { id: 237, category: 'WW2', weapon: 'RPG', price: '250k', demand: '7/10', availability: 'Unobtainable' },
  { id: 238, category: 'WW2', weapon: 'M249 SAW', price: '160k', demand: '6/10', availability: 'Unobtainable' },
  { id: 239, category: 'WW2', weapon: 'Barrett', price: '150k', demand: '3/10', availability: 'Unobtainable' },
  { id: 240, category: 'WW2', weapon: 'M1911', price: '120k', demand: '1/10', availability: 'Unobtainable' },
  
  // Prestige WW2 skins
  { id: 241, category: 'Prestige WW2', weapon: 'P90', price: '450k', demand: '9/10', availability: 'Unobtainable' },
  { id: 242, category: 'Prestige WW2', weapon: 'RPG', price: '400k', demand: '8/10', availability: 'Unobtainable' },
  { id: 243, category: 'Prestige WW2', weapon: 'M249 SAW', price: '250k', demand: '7/10', availability: 'Unobtainable' },
  { id: 244, category: 'Prestige WW2', weapon: 'Barrett', price: '220k', demand: '5/10', availability: 'Unobtainable' },
  { id: 245, category: 'Prestige WW2', weapon: 'M1911', price: '130k', demand: '1/10', availability: 'Unobtainable' },
  
  // Gingerbread skins
  { id: 246, category: 'Gingerbread', weapon: 'RPG', price: '240k', demand: '6/10', availability: 'Unobtainable' },
  { id: 247, category: 'Gingerbread', weapon: 'Scar L', price: '220k', demand: '5.5/10', availability: 'Unobtainable' },
  { id: 248, category: 'Gingerbread', weapon: 'AS Val', price: '190k', demand: '5/10', availability: 'Unobtainable' },
  { id: 249, category: 'Gingerbread', weapon: 'P90', price: '185k', demand: '4.5/10', availability: 'Unobtainable' },
  { id: 250, category: 'Gingerbread', weapon: 'Barrett', price: '170k', demand: '3/10', availability: 'Unobtainable' },
  { id: 251, category: 'Gingerbread', weapon: 'M1 Garand', price: '160k', demand: '4/10', availability: 'Unobtainable' },
  
  // Freeze Ray skins
  { id: 252, category: 'Freeze Ray', weapon: 'RPG', price: '210k', demand: '6/10', availability: 'Unobtainable' },
  { id: 253, category: 'Freeze Ray', weapon: 'Scar L', price: '200k', demand: '6/10', availability: 'Unobtainable' },
  { id: 254, category: 'Freeze Ray', weapon: 'AUG', price: '180k', demand: '5/10', availability: 'Unobtainable' },
  { id: 255, category: 'Freeze Ray', weapon: 'P90', price: '130k', demand: '4/10', availability: 'Unobtainable' },
  { id: 256, category: 'Freeze Ray', weapon: 'MP7', price: '125k', demand: '2/10', availability: 'Unobtainable' },
  
  // Void Ray skins
  { id: 257, category: 'Void Ray', weapon: 'RPG', price: '280k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 258, category: 'Void Ray', weapon: 'Scar L', price: '275k', demand: '7/10', availability: 'Unobtainable' },
  { id: 259, category: 'Void Ray', weapon: 'AUG', price: '255k', demand: '6/10', availability: 'Unobtainable' },
  { id: 260, category: 'Void Ray', weapon: 'P90', price: '230k', demand: '5.5/10', availability: 'Unobtainable' },
  { id: 261, category: 'Void Ray', weapon: 'MP7', price: '200k', demand: '5/10', availability: 'Unobtainable' },
  
  // Hyperlaser skins
  { id: 262, category: 'Hyperlaser', weapon: 'Scar L', price: '260k', demand: '8/10', availability: 'Unobtainable' },
  { id: 263, category: 'Hyperlaser', weapon: 'RPG', price: '240k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 264, category: 'Hyperlaser', weapon: 'AS Val', price: '210k', demand: '7/10', availability: 'Unobtainable' },
  { id: 265, category: 'Hyperlaser', weapon: 'M4A1', price: '195k', demand: '6.5/10', availability: 'Unobtainable' },
  { id: 266, category: 'Hyperlaser', weapon: 'Mossberg', price: '150k', demand: '4/10', availability: 'Unobtainable' },
  
  // Rainbow Laser skins
  { id: 267, category: 'Rainbow Laser', weapon: 'Scar L', price: '400k', demand: '9/10', availability: 'Unobtainable' },
  { id: 268, category: 'Rainbow Laser', weapon: 'RPG', price: '340k', demand: '8/10', availability: 'Unobtainable' },
  { id: 269, category: 'Rainbow Laser', weapon: 'AS Val', price: '300k', demand: '7/10', availability: 'Unobtainable' },
  { id: 270, category: 'Rainbow Laser', weapon: 'M4A1', price: '250k', demand: '5/10', availability: 'Unobtainable' },
  { id: 271, category: 'Rainbow Laser', weapon: 'Mossberg', price: '210k', demand: '3/10', availability: 'Unobtainable' },
  
  // Void Laser skins
  { id: 272, category: 'Void Laser', weapon: 'Scar L', price: '660k', demand: '8/10', availability: 'Unobtainable' },
  { id: 273, category: 'Void Laser', weapon: 'RPG', price: '600k', demand: '7/10', availability: 'Unobtainable' },
  { id: 274, category: 'Void Laser', weapon: 'AS Val', price: '520k', demand: '7/10', availability: 'Unobtainable' },
  { id: 275, category: 'Void Laser', weapon: 'M4A1', price: '460k', demand: '5/10', availability: 'Unobtainable' },
  { id: 276, category: 'Void Laser', weapon: 'Mossberg', price: '350k', demand: '2.5/10', availability: 'Unobtainable' },
  
  // Rose skins
  { id: 277, category: 'Rose', weapon: 'RPG', price: '250k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 278, category: 'Rose', weapon: 'Scar L', price: '230k', demand: '7/10', availability: 'Unobtainable' },
  { id: 279, category: 'Rose', weapon: 'P90', price: '190k', demand: '5.5/10', availability: 'Unobtainable' },
  { id: 280, category: 'Rose', weapon: 'M1 Garand', price: '165k', demand: '3.5/10', availability: 'Unobtainable' },
  { id: 281, category: 'Rose', weapon: 'Dragunov', price: '150k', demand: '3/10', availability: 'Unobtainable' },
  
  // Black Rose skins
  { id: 282, category: 'Black Rose', weapon: 'RPG', price: '340k', demand: '8/10', availability: 'Unobtainable' },
  { id: 283, category: 'Black Rose', weapon: 'Scar L', price: '300k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 284, category: 'Black Rose', weapon: 'P90', price: '250k', demand: '6/10', availability: 'Unobtainable' },
  { id: 285, category: 'Black Rose', weapon: 'M1 Garand', price: '220k', demand: '4/10', availability: 'Unobtainable' },
  { id: 286, category: 'Black Rose', weapon: 'Dragunov', price: '200k', demand: '3.5/10', availability: 'Unobtainable' },
  
  // Lucky Clover skins
  { id: 287, category: 'Lucky Clover', weapon: 'FN FAL', price: '250k', demand: '8/10', availability: 'Unobtainable' },
  { id: 288, category: 'Lucky Clover', weapon: 'Scar L', price: '240k', demand: '7.5/10', availability: 'Unobtainable' },
  { id: 289, category: 'Lucky Clover', weapon: 'RPG', price: '230k', demand: '7/10', availability: 'Unobtainable' },
  { id: 290, category: 'Lucky Clover', weapon: 'P90', price: '200k', demand: '6/10', availability: 'Unobtainable' },
  { id: 291, category: 'Lucky Clover', weapon: 'RPK', price: '195k', demand: '5/10', availability: 'Unobtainable' },
  
  // Fortune Favor skins
  { id: 292, category: 'Fortune Favor', weapon: 'FN FAL', price: '350k', demand: '9/10', availability: 'Unobtainable' },
  { id: 293, category: 'Fortune Favor', weapon: 'RPG', price: '330k', demand: '8/10', availability: 'Unobtainable' },
  { id: 294, category: 'Fortune Favor', weapon: 'Scar L', price: '310k', demand: '8.5/10', availability: 'Unobtainable' },
  { id: 295, category: 'Fortune Favor', weapon: 'P90', price: '260k', demand: '7/10', availability: 'Unobtainable' },
  { id: 296, category: 'Fortune Favor', weapon: 'RPK', price: '235k', demand: '6.5/10', availability: 'Unobtainable' },
  
  // Pixel skins
  { id: 297, category: 'Pixel', weapon: 'Scar L', price: '250k', demand: '7/10', availability: 'Unobtainable' },
  { id: 298, category: 'Pixel', weapon: 'RPG', price: '220k', demand: '6/10', availability: 'Unobtainable' },
  { id: 299, category: 'Pixel', weapon: 'FN FAL', price: '200k', demand: '6/10', availability: 'Unobtainable' },
  { id: 300, category: 'Pixel', weapon: 'P90', price: '190k', demand: '6/10', availability: 'Unobtainable' },
  { id: 301, category: 'Pixel', weapon: 'Barrett M107', price: '170k', demand: '4/10', availability: 'Unobtainable' },
  { id: 302, category: 'Pixel', weapon: 'AK-47', price: '50k', demand: '2/10', availability: 'Unobtainable' },
  
  // Diamond Pixel skins
  { id: 303, category: 'Diamond Pixel', weapon: 'AK-47', price: '400k', demand: '4/10', availability: 'Unobtainable' },
  { id: 304, category: 'Diamond Pixel', weapon: 'Scar L', price: '350k', demand: '8/10', availability: 'Unobtainable' },
  { id: 305, category: 'Diamond Pixel', weapon: 'FN FAL', price: '320k', demand: '8/10', availability: 'Unobtainable' },
  { id: 306, category: 'Diamond Pixel', weapon: 'RPG', price: '300k', demand: '7/10', availability: 'Unobtainable' },
  { id: 307, category: 'Diamond Pixel', weapon: 'P90', price: '300k', demand: '7/10', availability: 'Unobtainable' },
  { id: 308, category: 'Diamond Pixel', weapon: 'Barrett M107', price: '250k', demand: '6/10', availability: 'Unobtainable' },
  
  // === OBTAINABLE SKINS ===
  
  // Dark Matter skins - Most common obtainable
  { id: 309, category: 'Dark Matter', weapon: 'Scar L', price: '300k', demand: '8/10', availability: 'Obtainable' },
  { id: 310, category: 'Dark Matter', weapon: 'P90', price: '260k', demand: '7/10', availability: 'Obtainable' },
  { id: 311, category: 'Dark Matter', weapon: 'RPG', price: '250k', demand: '6/10', availability: 'Obtainable' },
  { id: 312, category: 'Dark Matter', weapon: 'AS Val', price: '200k', demand: '6/10', availability: 'Obtainable' },
  { id: 313, category: 'Dark Matter', weapon: 'Aug', price: '200k', demand: '6.5/10', availability: 'Obtainable' },
  { id: 314, category: 'Dark Matter', weapon: 'Barrett M107', price: '190k', demand: '4/10', availability: 'Obtainable' },
  { id: 315, category: 'Dark Matter', weapon: 'M4A1', price: '170k', demand: '5/10', availability: 'Obtainable' },
  { id: 316, category: 'Dark Matter', weapon: 'AK-47', price: '170k', demand: '4/10', availability: 'Obtainable' },
  { id: 317, category: 'Dark Matter', weapon: 'MP7', price: '150k', demand: '3/10', availability: 'Obtainable' },
  { id: 318, category: 'Dark Matter', weapon: 'Tommy Gun', price: '150k', demand: '5/10', availability: 'Obtainable' },
  { id: 319, category: 'Dark Matter', weapon: 'Sawn Off', price: '140k', demand: '2/10', availability: 'Obtainable' },
  { id: 320, category: 'Dark Matter', weapon: 'Double Barrel', price: '130k', demand: '2/10', availability: 'Obtainable' },
  { id: 321, category: 'Dark Matter', weapon: 'RPK', price: '130k', demand: '3/10', availability: 'Obtainable' },
  { id: 322, category: 'Dark Matter', weapon: 'M249 SAW', price: '125k', demand: '3/10', availability: 'Obtainable' },
  { id: 323, category: 'Dark Matter', weapon: 'Deagle', price: '120k', demand: '2/10', availability: 'Obtainable' },
  { id: 324, category: 'Dark Matter', weapon: 'Python', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 325, category: 'Dark Matter', weapon: 'Glock', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 326, category: 'Dark Matter', weapon: 'Glock 18', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 327, category: 'Dark Matter', weapon: 'M1911', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 328, category: 'Dark Matter', weapon: 'USP 45', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 329, category: 'Dark Matter', weapon: 'Mossberg', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 330, category: 'Dark Matter', weapon: 'Saiga', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 331, category: 'Dark Matter', weapon: 'Stagecoach', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 332, category: 'Dark Matter', weapon: 'AR-15', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 333, category: 'Dark Matter', weapon: 'Uzi', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 334, category: 'Dark Matter', weapon: 'Dragunov', price: '120k', demand: '1/10', availability: 'Obtainable' },
  
  // Anti Matter skins - Obtainable
  { id: 335, category: 'Anti Matter', weapon: 'Scar L', price: '280k', demand: '7.5/10', availability: 'Obtainable' },
  { id: 336, category: 'Anti Matter', weapon: 'P90', price: '240k', demand: '7/10', availability: 'Obtainable' },
  { id: 337, category: 'Anti Matter', weapon: 'RPG', price: '230k', demand: '6/10', availability: 'Obtainable' },
  { id: 338, category: 'Anti Matter', weapon: 'AS Val', price: '180k', demand: '6/10', availability: 'Obtainable' },
  { id: 339, category: 'Anti Matter', weapon: 'Aug', price: '180k', demand: '6.5/10', availability: 'Obtainable' },
  { id: 340, category: 'Anti Matter', weapon: 'Barrett M107', price: '170k', demand: '4/10', availability: 'Obtainable' },
  { id: 341, category: 'Anti Matter', weapon: 'M4A1', price: '150k', demand: '5/10', availability: 'Obtainable' },
  { id: 342, category: 'Anti Matter', weapon: 'AK-47', price: '150k', demand: '4/10', availability: 'Obtainable' },
  { id: 343, category: 'Anti Matter', weapon: 'MP7', price: '130k', demand: '3/10', availability: 'Obtainable' },
  { id: 344, category: 'Anti Matter', weapon: 'Tommy Gun', price: '130k', demand: '4.5/10', availability: 'Obtainable' },
  { id: 345, category: 'Anti Matter', weapon: 'Sawn Off', price: '120k', demand: '2/10', availability: 'Obtainable' },
  { id: 346, category: 'Anti Matter', weapon: 'Double Barrel', price: '120k', demand: '2/10', availability: 'Obtainable' },
  { id: 347, category: 'Anti Matter', weapon: 'RPK', price: '120k', demand: '3/10', availability: 'Obtainable' },
  { id: 348, category: 'Anti Matter', weapon: 'M249 SAW', price: '120k', demand: '3/10', availability: 'Obtainable' },
  { id: 349, category: 'Anti Matter', weapon: 'Deagle', price: '120k', demand: '2/10', availability: 'Obtainable' },
  { id: 350, category: 'Anti Matter', weapon: 'Python', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 351, category: 'Anti Matter', weapon: 'Glock', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 352, category: 'Anti Matter', weapon: 'Glock 18', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 353, category: 'Anti Matter', weapon: 'M1911', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 354, category: 'Anti Matter', weapon: 'USP 45', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 355, category: 'Anti Matter', weapon: 'Mossberg', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 356, category: 'Anti Matter', weapon: 'Saiga', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 357, category: 'Anti Matter', weapon: 'Stagecoach', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 358, category: 'Anti Matter', weapon: 'AR-15', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 359, category: 'Anti Matter', weapon: 'Uzi', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 360, category: 'Anti Matter', weapon: 'Dragunov', price: '120k', demand: '1/10', availability: 'Obtainable' },
  
  // Crimson Blood skins - Obtainable
  { id: 361, category: 'Crimson Blood', weapon: 'Scar L', price: '320k', demand: '8/10', availability: 'Obtainable' },
  { id: 362, category: 'Crimson Blood', weapon: 'Aug', price: '275k', demand: '7.5/10', availability: 'Obtainable' },
  { id: 363, category: 'Crimson Blood', weapon: 'AS Val', price: '245k', demand: '7/10', availability: 'Obtainable' },
  { id: 364, category: 'Crimson Blood', weapon: 'M4A1', price: '220k', demand: '6.5/10', availability: 'Obtainable' },
  { id: 365, category: 'Crimson Blood', weapon: 'AK-47', price: '195k', demand: '5/10', availability: 'Obtainable' },
  { id: 366, category: 'Crimson Blood', weapon: 'Sawn Off', price: '170k', demand: '5.5/10', availability: 'Obtainable' },
  { id: 367, category: 'Crimson Blood', weapon: 'Double Barrel', price: '155k', demand: '4.5/10', availability: 'Obtainable' },
  { id: 368, category: 'Crimson Blood', weapon: 'RPK', price: '150k', demand: '4/10', availability: 'Obtainable' },
  { id: 369, category: 'Crimson Blood', weapon: 'Deagle', price: '145k', demand: '3/10', availability: 'Obtainable' },
  { id: 370, category: 'Crimson Blood', weapon: 'Uzi', price: '135k', demand: '2/10', availability: 'Obtainable' },
  { id: 371, category: 'Crimson Blood', weapon: 'Glock', price: '120k', demand: '1/10', availability: 'Obtainable' },
  { id: 372, category: 'Crimson Blood', weapon: 'Glock 18', price: '120k', demand: '1.5/10', availability: 'Obtainable' },
  { id: 373, category: 'Crimson Blood', weapon: 'AR-15', price: '120k', demand: '1/10', availability: 'Obtainable' },
  
  // Void Reaper skins - Obtainable
  { id: 374, category: 'Void Reaper', weapon: 'AS Val', price: '250k', demand: '6/10', availability: 'Obtainable' },
  { id: 375, category: 'Void Reaper', weapon: 'Glock', price: '120k', demand: '1/10', availability: 'Obtainable' },
  
  // === LIMITED TIME SKINS ===
  
  // Easter Egg skins - Event limited
  { id: 376, category: 'Easter Egg', weapon: 'RPG', price: '175k', demand: '5.5/10', availability: 'Limited Time' },
  { id: 377, category: 'Easter Egg', weapon: 'AS Val', price: '145k', demand: '4/10', availability: 'Limited Time' },
  { id: 378, category: 'Easter Egg', weapon: 'Aug', price: '135k', demand: '4.5/10', availability: 'Limited Time' },
  { id: 379, category: 'Easter Egg', weapon: 'Barrett', price: '125k', demand: '2.5/10', availability: 'Limited Time' },
  { id: 380, category: 'Easter Egg', weapon: 'M4A1', price: '105k', demand: '4/10', availability: 'Limited Time' },
  { id: 381, category: 'Easter Egg', weapon: 'AK-47', price: '95k', demand: '2.5/10', availability: 'Limited Time' },
  { id: 382, category: 'Easter Egg', weapon: 'Riot Shield', price: '95k', demand: '2.5/10', availability: 'Limited Time' },
  { id: 383, category: 'Easter Egg', weapon: 'Tommy Gun', price: '85k', demand: '3.5/10', availability: 'Limited Time' },
  { id: 384, category: 'Easter Egg', weapon: 'M249 SAW', price: '60k', demand: '1.5/10', availability: 'Limited Time' },
  { id: 385, category: 'Easter Egg', weapon: 'Sawn Off', price: '55k', demand: '2/10', availability: 'Limited Time' },
  { id: 386, category: 'Easter Egg', weapon: 'MP7', price: '50k', demand: '2/10', availability: 'Limited Time' },
  { id: 387, category: 'Easter Egg', weapon: 'RPK', price: '50k', demand: '1.5/10', availability: 'Limited Time' },
  { id: 388, category: 'Easter Egg', weapon: 'Double Barrel', price: '50k', demand: '1/10', availability: 'Limited Time' },
  { id: 389, category: 'Easter Egg', weapon: 'Flamethrower', price: '40k', demand: '1/10', availability: 'Limited Time' },
  { id: 390, category: 'Easter Egg', weapon: 'Deagle', price: '35k', demand: '1/10', availability: 'Limited Time' },
  { id: 391, category: 'Easter Egg', weapon: 'AR-15', price: '35k', demand: '1/10', availability: 'Limited Time' },
  { id: 392, category: 'Easter Egg', weapon: 'Uzi', price: '35k', demand: '1/10', availability: 'Limited Time' },
  { id: 393, category: 'Easter Egg', weapon: 'Mossberg', price: '30k', demand: '1/10', availability: 'Limited Time' },
  { id: 394, category: 'Easter Egg', weapon: 'Dragunov', price: '30k', demand: '1/10', availability: 'Limited Time' },
  { id: 395, category: 'Easter Egg', weapon: 'Saiga 12', price: '30k', demand: '1/10', availability: 'Limited Time' },
  { id: 396, category: 'Easter Egg', weapon: 'Stagecoach', price: '25k', demand: '1/10', availability: 'Limited Time' },
  { id: 397, category: 'Easter Egg', weapon: 'Glock', price: '25k', demand: '1/10', availability: 'Limited Time' },
  { id: 398, category: 'Easter Egg', weapon: 'Glock 18', price: '25k', demand: '1/10', availability: 'Limited Time' },
  { id: 399, category: 'Easter Egg', weapon: 'Python', price: '20k', demand: '1/10', availability: 'Limited Time' },
  { id: 400, category: 'Easter Egg', weapon: 'M1911', price: '20k', demand: '1/10', availability: 'Limited Time' },
  { id: 401, category: 'Easter Egg', weapon: 'USP 45', price: '20k', demand: '1/10', availability: 'Limited Time' }
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
    <button @click="props.showHome" class="text-2xl font-bold text-purple-300 hover:text-white transition">Stack's Ohio Values</button>
    <!-- Desktop Menu -->
    <div class="hidden lg:flex space-x-6">
      <button @click="props.showHome" class="text-purple-300 hover:text-white transition">Home</button>
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
      <button @click="props.showHome(); isMenuOpen = false" class="block w-full text-left px-4 py-2 text-purple-300 hover:bg-purple-700 hover:text-white rounded transition">Home</button>
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
