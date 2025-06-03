<script setup>
import { ref } from 'vue';

const isMenuOpen = ref(false);
</script>

<template>
  <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ohio Values - Trading Hub</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <style>
        /* Custom animations and gradients */
        @keyframes float {
            0%, 100% { transform: translateY(0px); }
            50% { transform: translateY(-20px); }
        }
        @keyframes glow {
            0%, 100% { box-shadow: 0 0 20px rgba(236, 72, 153, 0.5); }
            50% { box-shadow: 0 0 40px rgba(236, 72, 153, 0.8), 0 0 60px rgba(236, 72, 153, 0.5); }
        }
        .animate-float { animation: float 3s ease-in-out infinite; }
        .animate-glow { animation: glow 2s ease-in-out infinite; }
        .gradient-text {
            background: linear-gradient(to right, #ec4899, #8b5cf6, #3b82f6);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }
        .gradient-bg {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        }
        .dark-gradient {
            background: linear-gradient(180deg, #0f0f0f 0%, #1a1a1a 100%);
        }
        .card-hover {
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .card-hover:hover {
            transform: translateY(-8px) scale(1.02);
        }
        .neon-border {
            box-shadow: 0 0 0 1px rgba(236, 72, 153, 0.5),
                        0 0 20px rgba(236, 72, 153, 0.3),
                        0 0 40px rgba(236, 72, 153, 0.1);
        }
        /* Scrollbar styling */
        ::-webkit-scrollbar {
            width: 10px;
        }
        ::-webkit-scrollbar-track {
            background: #1a1a1a;
        }
        ::-webkit-scrollbar-thumb {
            background: linear-gradient(to bottom, #ec4899, #8b5cf6);
            border-radius: 5px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: linear-gradient(to bottom, #db2777, #7c3aed);
        }
    </style>
</head>
<body class="bg-black text-white overflow-x-hidden">
    <div id="app">
        <!-- Loading Screen -->
        <div v-if="loading" class="fixed inset-0 bg-black z-50 flex items-center justify-center">
            <div class="text-6xl font-bold gradient-text animate-pulse">OHIO VALUES</div>
        </div>

        <!-- Navigation -->
        <nav class="fixed top-0 w-full bg-black/90 backdrop-blur-lg z-40 border-b border-pink-500/30">
            <div class="container mx-auto px-4 py-4 flex justify-between items-center">
                <div class="text-3xl font-bold gradient-text cursor-pointer" @click="scrollToTop">OHIO VALUES</div>
                <div class="hidden md:flex space-x-8">
                    <a href="#values" class="hover:text-pink-500 transition-colors">Values</a>
                    <a href="#calculator" class="hover:text-pink-500 transition-colors">Calculator</a>
                    <a href="#trending" class="hover:text-pink-500 transition-colors">Trending</a>
                    <a href="#stats" class="hover:text-pink-500 transition-colors">Stats</a>
                </div>
                <button @click="mobileMenu = !mobileMenu" class="md:hidden text-2xl">☰</button>
            </div>
            
            <!-- Mobile Menu -->
            <div v-if="mobileMenu" class="md:hidden bg-black/95 border-t border-pink-500/30 px-4 py-4 space-y-4">
                <a href="#values" class="block hover:text-pink-500">Values</a>
                <a href="#calculator" class="block hover:text-pink-500">Calculator</a>
                <a href="#trending" class="block hover:text-pink-500">Trending</a>
                <a href="#stats" class="block hover:text-pink-500">Stats</a>
            </div>
        </nav>

        <!-- Hero Section -->
        <section class="min-h-screen flex items-center justify-center relative mt-16">
            <div class="absolute inset-0 bg-gradient-to-br from-pink-500/10 via-purple-500/5 to-blue-500/10"></div>
            <div class="container mx-auto px-4 text-center z-10">
                <h1 class="text-6xl md:text-8xl font-bold gradient-text animate-float mb-6">OHIO VALUES</h1>
                <p class="text-xl md:text-2xl text-gray-300 mb-8">The Ultimate Roblox Ohio Trading Platform</p>
                <div class="flex flex-col md:flex-row gap-4 justify-center items-center">
                    <input 
                        v-model="searchQuery"
                        @input="searchSkins"
                        type="text" 
                        placeholder="Search skins..." 
                        class="w-full md:w-96 px-6 py-4 bg-gray-900/50 border border-pink-500/50 rounded-full focus:outline-none focus:border-pink-500 focus:shadow-lg focus:shadow-pink-500/25 transition-all"
                    >
                    <button class="gradient-bg px-8 py-4 rounded-full font-bold hover:shadow-lg hover:shadow-purple-500/50 transition-all">
                        Search
                    </button>
                </div>
                <div class="mt-12 grid grid-cols-2 md:grid-cols-4 gap-6 max-w-4xl mx-auto">
                    <div class="bg-gray-900/30 backdrop-blur border border-pink-500/30 rounded-xl p-6 card-hover">
                        <div class="text-3xl font-bold text-pink-500">{{ totalSkins }}</div>
                        <div class="text-gray-400">Total Skins</div>
                    </div>
                    <div class="bg-gray-900/30 backdrop-blur border border-purple-500/30 rounded-xl p-6 card-hover">
                        <div class="text-3xl font-bold text-purple-500">{{ activeUsers }}</div>
                        <div class="text-gray-400">Active Traders</div>
                    </div>
                    <div class="bg-gray-900/30 backdrop-blur border border-blue-500/30 rounded-xl p-6 card-hover">
                        <div class="text-3xl font-bold text-blue-500">{{ dailyTrades }}</div>
                        <div class="text-gray-400">Daily Trades</div>
                    </div>
                    <div class="bg-gray-900/30 backdrop-blur border border-green-500/30 rounded-xl p-6 card-hover">
                        <div class="text-3xl font-bold text-green-500">{{ marketCap }}</div>
                        <div class="text-gray-400">Market Cap</div>
                    </div>
                </div>
            </div>
        </section>

        <!-- Values Section -->
        <section id="values" class="py-20">
            <div class="container mx-auto px-4">
                <h2 class="text-4xl md:text-5xl font-bold text-center gradient-text mb-12">Skin Values</h2>
                
                <!-- Category Tabs -->
                <div class="flex flex-wrap justify-center gap-4 mb-12">
                    <button 
                        v-for="category in categories" 
                        :key="category"
                        @click="selectedCategory = category"
                        :class="[
                            'px-6 py-3 rounded-full font-semibold transition-all',
                            selectedCategory === category 
                                ? 'gradient-bg text-white shadow-lg shadow-purple-500/30' 
                                : 'bg-gray-900/50 border border-gray-700 hover:border-pink-500'
                        ]"
                    >
                        {{ category }}
                    </button>
                </div>

                <!-- Skins Grid -->
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
                    <div 
                        v-for="skin in filteredSkins" 
                        :key="skin.id"
                        @click="addToCalculator(skin)"
                        class="bg-gray-900/50 backdrop-blur border border-gray-800 rounded-xl p-6 card-hover cursor-pointer group"
                    >
                        <!-- Demand Badge -->
                        <div class="flex justify-between items-start mb-4">
                            <span 
                                :class="[
                                    'px-3 py-1 rounded-full text-xs font-bold',
                                    skin.demand === 'High' ? 'bg-red-500/20 text-red-500' :
                                    skin.demand === 'Medium' ? 'bg-yellow-500/20 text-yellow-500' :
                                    'bg-green-500/20 text-green-500'
                                ]"
                            >
                                {{ skin.demand }} Demand
                            </span>
                            <span 
                                :class="[
                                    'text-sm font-bold',
                                    skin.trend === 'up' ? 'text-green-500' :
                                    skin.trend === 'down' ? 'text-red-500' :
                                    'text-gray-500'
                                ]"
                            >
                                {{ skin.trend === 'up' ? '↑' : skin.trend === 'down' ? '↓' : '→' }}
                                {{ skin.changePercent }}%
                            </span>
                        </div>
                        
                        <!-- Skin Image Placeholder -->
                        <div class="w-full h-32 bg-gradient-to-br from-pink-500/20 to-purple-500/20 rounded-lg mb-4 flex items-center justify-center text-4xl group-hover:from-pink-500/30 group-hover:to-purple-500/30 transition-all">
                            {{ skin.emoji }}
                        </div>
                        
                        <!-- Skin Info -->
                        <h3 class="text-lg font-bold mb-2">{{ skin.name }}</h3>
                        <div class="flex justify-between items-center">
                            <span class="text-sm text-gray-400">{{ skin.category }}</span>
                            <span class="text-2xl font-bold gradient-text">${{ skin.value.toLocaleString() }}</span>
                        </div>
                        
                        <!-- Quick Actions -->
                        <div class="mt-4 opacity-0 group-hover:opacity-100 transition-opacity">
                            <button class="w-full py-2 bg-pink-500/20 hover:bg-pink-500/30 rounded-lg text-pink-500 font-semibold transition-all">
                                Add to Trade
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- Trade Calculator -->
        <section id="calculator" class="py-20 bg-gray-900/30">
            <div class="container mx-auto px-4">
                <h2 class="text-4xl md:text-5xl font-bold text-center gradient-text mb-12">Trade Calculator</h2>
                
                <div class="max-w-6xl mx-auto bg-gray-900/50 backdrop-blur border border-pink-500/30 rounded-2xl p-8 neon-border">
                    <div class="grid md:grid-cols-2 gap-8">
                        <!-- Your Offer -->
                        <div class="space-y-4">
                            <h3 class="text-2xl font-bold text-pink-500 mb-4">Your Offer</h3>
                            <div class="space-y-2 min-h-[200px] bg-black/30 rounded-xl p-4">
                                <div 
                                    v-for="(item, index) in yourOffer" 
                                    :key="index"
                                    class="flex justify-between items-center bg-gray-800/50 rounded-lg p-3"
                                >
                                    <span>{{ item.name }}</span>
                                    <div class="flex items-center gap-3">
                                        <span class="text-pink-500">${{ item.value.toLocaleString() }}</span>
                                        <button 
                                            @click="removeFromYourOffer(index)"
                                            class="text-red-500 hover:text-red-400"
                                        >
                                            ✕
                                        </button>
                                    </div>
                                </div>
                                <div v-if="yourOffer.length === 0" class="text-center text-gray-500 py-8">
                                    Click skins to add them here
                                </div>
                            </div>
                            <div class="text-xl font-bold">
                                Total: <span class="gradient-text">${{ yourTotal.toLocaleString() }}</span>
                            </div>
                        </div>
                        
                        <!-- Their Offer -->
                        <div class="space-y-4">
                            <h3 class="text-2xl font-bold text-purple-500 mb-4">Their Offer</h3>
                            <div class="space-y-2 min-h-[200px] bg-black/30 rounded-xl p-4">
                                <div 
                                    v-for="(item, index) in theirOffer" 
                                    :key="index"
                                    class="flex justify-between items-center bg-gray-800/50 rounded-lg p-3"
                                >
                                    <span>{{ item.name }}</span>
                                    <div class="flex items-center gap-3">
                                        <span class="text-purple-500">${{ item.value.toLocaleString() }}</span>
                                        <button 
                                            @click="removeFromTheirOffer(index)"
                                            class="text-red-500 hover:text-red-400"
                                        >
                                            ✕
                                        </button>
                                    </div>
                                </div>
                                <div v-if="theirOffer.length === 0" class="text-center text-gray-500 py-8">
                                    Click skins to add them here
                                </div>
                            </div>
                            <div class="text-xl font-bold">
                                Total: <span class="gradient-text">${{ theirTotal.toLocaleString() }}</span>
                            </div>
                        </div>
                    </div>
                    
                    <!-- Trade Analysis -->
                    <div class="mt-8 text-center">
                        <div class="text-3xl font-bold mb-4" :class="tradeStatusColor">
                            {{ tradeStatus }}
                        </div>
                        <div class="text-gray-400">
                            {{ tradeAnalysis }}
                        </div>
                        <button 
                            @click="completeTrade"
                            :disabled="yourOffer.length === 0 || theirOffer.length === 0"
                            class="mt-6 px-8 py-3 gradient-bg rounded-full font-bold disabled:opacity-50 disabled:cursor-not-allowed hover:shadow-lg hover:shadow-purple-500/50 transition-all"
                        >
                            Complete Trade
                        </button>
                    </div>
                </div>
                
                <!-- Trade Mode Toggle -->
                <div class="text-center mt-8">
                    <button 
                        @click="tradeMode = tradeMode === 'your' ? 'their' : 'your'"
                        class="px-6 py-3 bg-gray-800 rounded-full hover:bg-gray-700 transition-all"
                    >
                        Currently adding to: 
                        <span :class="tradeMode === 'your' ? 'text-pink-500' : 'text-purple-500'" class="font-bold">
                            {{ tradeMode === 'your' ? 'Your Offer' : 'Their Offer' }}
                        </span>
                    </button>
                </div>
            </div>
        </section>

        <!-- Trending Section -->
        <section id="trending" class="py-20">
            <div class="container mx-auto px-4">
                <h2 class="text-4xl md:text-5xl font-bold text-center gradient-text mb-12">Trending Now 🔥</h2>
                
                <div class="grid md:grid-cols-2 lg:grid-cols-3 gap-8">
                    <!-- Top Gainers -->
                    <div class="bg-gray-900/50 backdrop-blur border border-green-500/30 rounded-xl p-6">
                        <h3 class="text-2xl font-bold text-green-500 mb-4">Top Gainers 📈</h3>
                        <div class="space-y-3">
                            <div v-for="skin in topGainers" :key="skin.id" class="flex justify-between items-center">
                                <span>{{ skin.name }}</span>
                                <span class="text-green-500 font-bold">+{{ skin.changePercent }}%</span>
                            </div>
                        </div>
                    </div>
                    
                    <!-- Top Losers -->
                    <div class="bg-gray-900/50 backdrop-blur border border-red-500/30 rounded-xl p-6">
                        <h3 class="text-2xl font-bold text-red-500 mb-4">Top Losers 📉</h3>
                        <div class="space-y-3">
                            <div v-for="skin in topLosers" :key="skin.id" class="flex justify-between items-center">
                                <span>{{ skin.name }}</span>
                                <span class="text-red-500 font-bold">{{ skin.changePercent }}%</span>
                            </div>
                        </div>
                    </div>
                    
                    <!-- Most Traded -->
                    <div class="bg-gray-900/50 backdrop-blur border border-purple-500/30 rounded-xl p-6">
                        <h3 class="text-2xl font-bold text-purple-500 mb-4">Most Traded 🔄</h3>
                        <div class="space-y-3">
                            <div v-for="skin in mostTraded" :key="skin.id" class="flex justify-between items-center">
                                <span>{{ skin.name }}</span>
                                <span class="text-purple-500 font-bold">{{ skin.trades }} trades</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- Stats Section -->
        <section id="stats" class="py-20 bg-gray-900/30">
            <div class="container mx-auto px-4">
                <h2 class="text-4xl md:text-5xl font-bold text-center gradient-text mb-12">Market Stats</h2>
                
                <div class="grid md:grid-cols-2 lg:grid-cols-4 gap-6 max-w-6xl mx-auto">
                    <div class="bg-gray-900/50 backdrop-blur border border-pink-500/30 rounded-xl p-6 text-center">
                        <div class="text-4xl font-bold text-pink-500 mb-2">{{ stats.totalVolume }}</div>
                        <div class="text-gray-400">24h Volume</div>
                    </div>
                    <div class="bg-gray-900/50 backdrop-blur border border-purple-500/30 rounded-xl p-6 text-center">
                        <div class="text-4xl font-bold text-purple-500 mb-2">{{ stats.avgPrice }}</div>
                        <div class="text-gray-400">Average Price</div>
                    </div>
                    <div class="bg-gray-900/50 backdrop-blur border border-blue-500/30 rounded-xl p-6 text-center">
                        <div class="text-4xl font-bold text-blue-500 mb-2">{{ stats.priceChange }}%</div>
                        <div class="text-gray-400">Market Change</div>
                    </div>
                    <div class="bg-gray-900/50 backdrop-blur border border-green-500/30 rounded-xl p-6 text-center">
                        <div class="text-4xl font-bold text-green-500 mb-2">{{ stats.activeListings }}</div>
                        <div class="text-gray-400">Active Listings</div>
                    </div>
                </div>
            </div>
        </section>

        <!-- Notification -->
        <transition name="fade">
            <div 
                v-if="notification.show"
                :class="[
                    'fixed bottom-8 right-8 px-6 py-4 rounded-xl font-semibold shadow-lg z-50',
                    notification.type === 'success' ? 'bg-green-500 text-white' :
                    notification.type === 'error' ? 'bg-red-500 text-white' :
                    'bg-blue-500 text-white'
                ]"
            >
                {{ notification.message }}
            </div>
        </transition>
    </div>

    <script>
        const { createApp } = Vue;

        createApp({
            data() {
                return {
                    loading: true,
                    mobileMenu: false,
                    searchQuery: '',
                    selectedCategory: 'All',
                    tradeMode: 'your',
                    yourOffer: [],
                    theirOffer: [],
                    notification: {
                        show: false,
                        message: '',
                        type: 'info'
                    },
                    categories: ['All', 'Legendary', 'Epic', 'Rare', 'Uncommon', 'Common'],
                    skins: [
                        // Legendary Skins (Highest Demand)
                        { id: 1, name: 'Golden AK-47', category: 'Legendary', value: 150000, demand: 'High', trend: 'up', changePercent: 15, emoji: '🔫', trades: 523 },
                        { id: 2, name: 'Diamond AWP', category: 'Legendary', value: 200000, demand: 'High', trend: 'up', changePercent: 22, emoji: '💎', trades: 412 },
                        { id: 3, name: 'Obsidian Katana', category: 'Legendary', value: 180000, demand: 'High', trend: 'up', changePercent: 18, emoji: '⚔️', trades: 389 },
                        { id: 4, name: 'Phoenix Blaze', category: 'Legendary', value: 175000, demand: 'High', trend: 'stable', changePercent: 2, emoji: '🔥', trades: 367 },
                        { id: 5, name: 'Void Reaper', category: 'Legendary', value: 195000, demand: 'High', trend: 'up', changePercent: 25, emoji: '💀', trades: 445 },
                        
                        // Epic Skins (High Demand)
                        { id: 6, name: 'Neon Dragon', category: 'Epic', value: 85000, demand: 'High', trend: 'up', changePercent: 12, emoji: '🐉', trades: 312 },
                        { id: 7, name: 'Cyber Samurai', category: 'Epic', value: 78000, demand: 'Medium', trend: 'stable', changePercent: 0, emoji: '🤖', trades: 289 },
                        { id: 8, name: 'Arctic Wolf', category: 'Epic', value: 72000, demand: 'Medium', trend: 'down', changePercent: -5, emoji: '🐺', trades: 267 },
                        { id: 9, name: 'Molten Core', category: 'Epic', value: 80000, demand: 'High', trend: 'up', changePercent: 8, emoji: '🌋', trades: 334 },
                        { id: 10, name: 'Storm Bringer', category: 'Epic', value: 76000, demand: 'Medium', trend: 'up', changePercent: 6, emoji: '⚡', trades: 278 },
                        
                        // Rare Skins (Medium Demand)
                        { id: 11, name: 'Tactical P90', category: 'Rare', value: 35000, demand: 'Medium', trend: 'stable', changePercent: 1, emoji: '🎯', trades: 198 },
                        { id: 12, name: 'Urban Camo', category: 'Rare', value: 28000, demand: 'Low', trend: 'down', changePercent: -3, emoji: '🏙️', trades: 145 },
                        { id: 13, name: 'Desert Eagle', category: 'Rare', value: 32000, demand: 'Medium', trend: 'up', changePercent: 4, emoji: '🦅', trades: 223 },
                        { id: 14, name: 'Plasma Rifle', category: 'Rare', value: 38000, demand: 'Medium', trend: 'up', changePercent: 7, emoji: '💫', trades: 234 },
                        { id: 15, name: 'Shadow Strike', category: 'Rare', value: 30000, demand: 'Low', trend: 'stable', changePercent: 0, emoji: '🌑', trades: 167 },
                        
                        // Uncommon Skins (Low-Medium Demand)
                        { id: 16, name: 'Basic M4', category: 'Uncommon', value: 12000, demand: 'Low', trend: 'down', changePercent: -2, emoji: '🔫', trades: 89 },
                        { id: 17, name: 'Green Scout', category: 'Uncommon', value: 10000, demand: 'Low', trend: 'stable', changePercent: 0, emoji: '🟢', trades: 67 },
                        { id: 18, name: 'Blue Steel', category: 'Uncommon', value: 15000, demand: 'Medium', trend: 'up', changePercent: 3, emoji: '🔷', trades: 112 },
                        { id: 19, name: 'Red Dot', category: 'Uncommon', value: 11000, demand: 'Low', trend: 'down', changePercent: -1, emoji: '🔴', trades: 78 },
                        { id: 20, name: 'Gray Ghost', category: 'Uncommon', value: 13000, demand: 'Low', trend: 'stable', changePercent: 0, emoji: '👻', trades: 94 },
                        
                        // Common Skins (Low Demand)
                        { id: 21, name: 'Starter Pistol', category: 'Common', value: 5000, demand: 'Low', trend: 'stable', changePercent: 0, emoji: '🔫', trades: 45 },
                        { id: 22, name: 'Wooden Bat', category: 'Common', value: 3000, demand: 'Low', trend: 'down', changePercent: -1, emoji: '🏏', trades: 23 },
                        { id: 23, name: 'Basic Knife', category: 'Common', value: 4000, demand: 'Low', trend: 'stable', changePercent: 0, emoji: '🔪', trades: 34 },
                        { id: 24, name: 'Training Gloves', category: 'Common', value: 2500, demand: 'Low', trend: 'down', changePercent: -2, emoji: '🥊', trades: 12 },
                        { id: 25, name: 'Rookie Shield', category: 'Common', value: 6000, demand: 'Low', trend: 'up', changePercent: 1, emoji: '🛡️', trades: 56 },
                    ],
                    stats: {
                        totalVolume: '$2.5M',
                        avgPrice: '$45.2K',
                        priceChange: '+12.5',
                        activeListings: '1,234'
                    }
                };
            },
            computed: {
                filteredSkins() {
                    let skins = this.skins;
                    
                    // Filter by category
                    if (this.selectedCategory !== 'All') {
                        skins = skins.filter(skin => skin.category === this.selectedCategory);
                    }
                    
                    // Filter by search query
                    if (this.searchQuery) {
                        skins = skins.filter(skin => 
                            skin.name.toLowerCase().includes(this.searchQuery.toLowerCase())
                        );
                    }
                    
                    // Sort by demand (High > Medium > Low) then by value
                    return skins.sort((a, b) => {
                        const demandOrder = { 'High': 3, 'Medium': 2, 'Low': 1 };
                        if (demandOrder[a.demand] !== demandOrder[b.demand]) {
                            return demandOrder[b.demand] - demandOrder[a.demand];
                        }
                        return b.value - a.value;
                    });
                },
                yourTotal() {
                    return this.yourOffer.reduce((sum, item) => sum + item.value, 0);
                },
                theirTotal() {
                    return this.theirOffer.reduce((sum, item) => sum + item.value, 0);
                },
                tradeStatus() {
                    if (this.yourOffer.length === 0 || this.theirOffer.length === 0) {
                        return 'Add items to both sides';
                    }
                    const difference = this.theirTotal - this.yourTotal;
                    const percentage = Math.abs(difference) / Math.max(this.yourTotal, this.theirTotal) * 100;
                    
                    if (percentage < 5) return '✅ Fair Trade';
                    if (difference > 0) return '💰 You Win!';
                    return '⚠️ You Overpay';
                },
                tradeStatusColor() {
                    if (this.tradeStatus.includes('Fair')) return 'text-green-500';
                    if (this.tradeStatus.includes('Win')) return 'text-green-500';
                    if (this.tradeStatus.includes('Overpay')) return 'text-red-500';
                    return 'text-gray-500';
                },
                tradeAnalysis() {
                    if (this.yourOffer.length === 0 || this.theirOffer.length === 0) {
                        return 'Select skins from the values list to start trading';
                    }
                    const difference = Math.abs(this.theirTotal - this.yourTotal);
                    const percentage = (difference / Math.max(this.yourTotal, this.theirTotal) * 100).toFixed(1);
                    
                    if (percentage < 5) {
                        return `Trade difference: ${difference.toLocaleString()} (${percentage}%)`;
                    }
                    return `Trade difference: ${difference.toLocaleString()} (${percentage}%)`;
                },
                topGainers() {
                    return this.skins
                        .filter(skin => skin.trend === 'up')
                        .sort((a, b) => b.changePercent - a.changePercent)
                        .slice(0, 5);
                },
                topLosers() {
                    return this.skins
                        .filter(skin => skin.trend === 'down')
                        .sort((a, b) => a.changePercent - b.changePercent)
                        .slice(0, 5);
                },
                mostTraded() {
                    return [...this.skins]
                        .sort((a, b) => b.trades - a.trades)
                        .slice(0, 5);
                },
                totalSkins() {
                    return this.skins.length;
                },
                activeUsers() {
                    return '2,847';
                },
                dailyTrades() {
                    return '5,234';
                },
                marketCap() {
                    return '$45.2M';
                }
            },
            methods: {
                scrollToTop() {
                    window.scrollTo({ top: 0, behavior: 'smooth' });
                },
                searchSkins() {
                    // Search is handled by computed property
                },
                addToCalculator(skin) {
                    if (this.tradeMode === 'your') {
                        this.yourOffer.push({...skin});
                        this.showNotification(`Added ${skin.name} to your offer`, 'success');
                    } else {
                        this.theirOffer.push({...skin});
                        this.showNotification(`Added ${skin.name} to their offer`, 'success');
                    }
                },
                removeFromYourOffer(index) {
                    this.yourOffer.splice(index, 1);
                },
                removeFromTheirOffer(index) {
                    this.theirOffer.splice(index, 1);
                },
                completeTrade() {
                    if (this.yourOffer.length === 0 || this.theirOffer.length === 0) {
                        this.showNotification('Both sides need items to complete trade', 'error');
                        return;
                    }
                    
                    // Save to localStorage (in a real app, this would go to a backend)
                    const trade = {
                        timestamp: new Date().toISOString(),
                        yourOffer: this.yourOffer,
                        theirOffer: this.theirOffer,
                        yourTotal: this.yourTotal,
                        theirTotal: this.theirTotal,
                        profit: this.theirTotal - this.yourTotal
                    };
                    
                    let trades = JSON.parse(localStorage.getItem('ohioTrades') || '[]');
                    trades.push(trade);
                    localStorage.setItem('ohioTrades', JSON.stringify(trades));
                    
                    this.showNotification('Trade completed successfully!', 'success');
                    
                    // Reset
                    this.yourOffer = [];
                    this.theirOffer = [];
                },
                showNotification(message, type = 'info') {
                    this.notification = {
                        show: true,
                        message,
                        type
                    };
                    
                    setTimeout(() => {
                        this.notification.show = false;
                    }, 3000);
                }
            },
            mounted() {
                // Simulate loading
                setTimeout(() => {
                    this.loading = false;
                }, 1500);
                
                // Add keyboard shortcuts
                document.addEventListener('keydown', (e) => {
                    if (e.key === '/' && document.activeElement.tagName !== 'INPUT') {
                        e.preventDefault();
                        document.querySelector('input[type="text"]').focus();
                    }
                });
                
                // Update stats periodically (simulated)
                setInterval(() => {
                    // Simulate market changes
                    this.skins.forEach(skin => {
                        if (Math.random() > 0.7) {
                            const change = Math.random() * 10 - 5;
                            skin.changePercent = Math.round(skin.changePercent + change);
                            skin.value = Math.round(skin.value * (1 + change / 100));
                            
                            if (change > 2) skin.trend = 'up';
                            else if (change < -2) skin.trend = 'down';
                            else skin.trend = 'stable';
                        }
                    });
                }, 30000); // Update every 30 seconds
            }
        }).mount('#app');
    </script>

    <style>
        /* Vue transitions */
        .fade-enter-active, .fade-leave-active {
            transition: opacity 0.3s;
        }
        .fade-enter-from, .fade-leave-to {
            opacity: 0;
        }
    </style>
</body>
</html>
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
</style>
