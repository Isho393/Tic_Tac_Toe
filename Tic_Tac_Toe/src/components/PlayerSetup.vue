<script setup>
import { reactive, ref } from 'vue'

const p1 = reactive({
    name: '',
    symbol: '❌'
})

const p2 = reactive({
    name: '',
    symbol: '⭕'
})

const rounds = ref(1)

const emit = defineEmits(['startGame'])

function handleSubmit() {
    if (p1.symbol === p2.symbol) {
        alert("Chaque joueur doit avoir un symbole différent.")
        return
    }
    emit('startGame', { ...p1 }, { ...p2 }, rounds.value)
}
</script>



<template>
    <form @submit.prevent="handleSubmit" class="space-y-4">
        <div>
            <label class="block font-medium">Nom Joueur 1</label>
            <input v-model="p1.name" required class="w-full p-2 border border-gray-300 rounded" placeholder="Entrez le nom" />
        </div>
        <div>
            <label class="block font-medium">Symbole Joueur 1</label>
            <select v-model="p1.symbol" class="w-full p-2 border border-gray-300 rounded">
                <option>❌</option>
                <option>🔥</option>
                <option>💎</option>
                <option>🍕</option>
            </select>
        </div>

        <div>
            <label class="block font-medium">Nom Joueur 2</label>
            <input v-model="p2.name" required class="w-full p-2 border border-gray-300 rounded" placeholder="Entrez le nom" />
        </div>
        <div>
            <label class="block font-medium">Symbole Joueur 2</label>
            <select v-model="p2.symbol" class="w-full p-2 border border-gray-300 rounded">
                <option>⭕</option>
                <option>🌟</option>
                <option>🍀</option>
                <option>🎯</option>
            </select>
        </div>

        <div>
            <label class="block font-medium">Nombre de manches</label>
            <input type="number" v-model.number="rounds" min="1" class="w-full p-2 border border-gray-300 rounded" />
        </div>

        <button type="submit" class="w-full bg-blue-600 text-white py-2 rounded">Démarrer</button>
    </form>
</template>


