<template>
    <div>
      <div class="mb-4 text-center">
        <p class="font-bold">Manche {{ currentRound }} / {{ rounds }}</p>
        <p class="text-lg">
          Tour de :
          <span :class="currentPlayer === player1.symbol ? 'text-blue-600' : 'text-red-600'">
            {{ currentPlayerName }}
          </span>
        </p>
      </div>
  
      <div class="grid grid-cols-3 gap-2">
        <button
          v-for="(cell, index) in board"
          :key="index"
          class="h-20 w-20 text-3xl font-bold flex items-center justify-center border border-gray-300 rounded-lg bg-gray-50 hover:bg-gray-200"
          @click="makeMove(index)"
          :disabled="cell !== '' || winner"
        >
          {{ cell }}
        </button>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, computed, watch } from 'vue'
  
  const props = defineProps({
    player1: Object,
    player2: Object,
    rounds: Number
  })
  const emit = defineEmits(['roundEnd'])
  
  const board = ref(Array(9).fill(''))
  const currentPlayer = ref(props.player1.symbol)
  const currentRound = ref(1)
  const moveCount = ref(0)
  const winner = ref(null)
  
  // 🧠 Nom du joueur courant
  const currentPlayerName = computed(() => {
    return currentPlayer.value === props.player1.symbol
      ? props.player1.name
      : props.player2.name
  })
  
  // ✅ Vérifie si un joueur a gagné
  function checkWinner() {
    const winPatterns = [
      [0,1,2], [3,4,5], [6,7,8], // lignes
      [0,3,6], [1,4,7], [2,5,8], // colonnes
      [0,4,8], [2,4,6]           // diagonales
    ]
  
    for (const [a, b, c] of winPatterns) {
      if (
        board.value[a] &&
        board.value[a] === board.value[b] &&
        board.value[b] === board.value[c]
      ) {
        winner.value = board.value[a]
        return true
      }
    }
  
    return false
  }
  
  // ⚙️ Jouer un coup
  function makeMove(index) {
    if (board.value[index] !== '' || winner.value) return
    board.value[index] = currentPlayer.value
    moveCount.value++
  
    if (checkWinner()) {
      const playerName = currentPlayerName.value
      emit('roundEnd', {
        result: 'win',
        winner: playerName,
        symbol: currentPlayer.value,
        moves: moveCount.value,
        board: [...board.value]
      })
      return
    }
  
    if (moveCount.value === 9) {
      emit('roundEnd', {
        result: 'draw',
        moves: moveCount.value,
        board: [...board.value]
      })
      return
    }
  
    switchPlayer()
  }
  
  function switchPlayer() {
    currentPlayer.value =
      currentPlayer.value === props.player1.symbol
        ? props.player2.symbol
        : props.player1.symbol
  }
  </script>
  