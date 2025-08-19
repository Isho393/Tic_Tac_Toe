<template>
    <div class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50">
      <div class="bg-white p-6 rounded-xl w-full max-w-md text-center shadow-lg">
        <h2 class="text-2xl font-bold mb-4">
          {{ result.result === 'win' ? `${result.winner} a gagné ! 🏆` : "Match nul 🤝" }}
        </h2>
        <p v-if="result.result === 'win'" class="mb-2">
          Victoire en {{ result.moves }} coups
        </p>
  
        <div class="grid grid-cols-3 gap-2 my-4">
          <div
            v-for="(cell, index) in result.board"
            :key="index"
            class="h-16 w-16 flex items-center justify-center border text-2xl font-bold rounded-md bg-gray-50"
          >
            {{ cell }}
          </div>
        </div>
  
        <button class="bg-blue-600 text-white px-4 py-2 rounded mt-4" @click="$emit('next')">
          Manche suivante
        </button>
      </div>
    </div>
  </template>
  
  <script setup>
  defineProps({
    result: Object
  })
  defineEmits(['next'])
  </script>
  