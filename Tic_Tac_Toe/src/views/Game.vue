<script setup>
import { ref } from 'vue'
import PlayerSetup from '../components/PlayerSetup.vue'
import GameBoard from '../components/GameBoard.vue'
import ResultModal from '../components/ResultModal.vue'


const gameStarted = ref(false)
const player1 = ref({})
const player2 = ref({})
const rounds = ref(1)

const showResult = ref(false)
const lastResult = ref({})
const currentRound = ref(1)

function startGame(p1, p2, r) {
    player1.value = p1
    player2.value = p2
    rounds.value = r
    gameStarted.value = true
    currentRound.value = 1
}

const scores = ref({
    [player1.value.symbol]: 0,
    [player2.value.symbol]: 0,
    draw: 0
})


function handleRoundEnd(result) {
    lastResult.value = result
    showResult.value = true
}

function nextRound() {
    showResult.value = false
    currentRound.value++

    if (currentRound.value > rounds.value) {
        alert("Toutes les manches sont terminées ! 🎉")
        gameStarted.value = false
    }
}
</script>



<template>
    <div class="w-full max-w-md p-4 bg-white shadow-md rounded-xl">
        <h1 class="text-2xl font-bold mb-4 text-center">Tic-Tac-Toe</h1>

        <PlayerSetup v-if="!gameStarted" @startGame="startGame" />
        <div v-else>
            <GameBoard v-if="!showResult" :player1="player1" :player2="player2" :rounds="rounds"
                @roundEnd="handleRoundEnd" />
            <ResultModal v-if="showResult" :result="lastResult" @next="nextRound" />
        </div>
        
    </div>
</template>

