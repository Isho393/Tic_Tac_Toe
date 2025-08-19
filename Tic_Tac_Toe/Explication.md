GameBoard.vue

Voici une explication complète de ton composant Vue 3 en utilisant la **Composition API avec `<script setup>`**. Ce composant représente une **grille de jeu de morpion (Tic Tac Toe)** avec gestion du joueur courant, des manches et de la détection de victoire.

---

## 🧩 Partie `<template>`

```html
<template>
  <div>
    <!-- Affichage du numéro de manche et du joueur courant -->
    <div class="mb-4 text-center">
      <p class="font-bold">Manche {{ currentRound }} / {{ rounds }}</p>
      <p class="text-lg">
        Tour de :
        <span :class="currentPlayer === player1.symbol ? 'text-blue-600' : 'text-red-600'">
          {{ currentPlayerName }}
        </span>
      </p>
    </div>

    <!-- Grille 3x3 du jeu -->
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
```

### 💡 Que fait cette partie ?

* Affiche la **manche actuelle** et le **nombre total de manches**.
* Affiche le **nom du joueur en cours**, coloré selon le symbole (`X` ou `O`).
* Affiche une **grille 3x3** avec un bouton par cellule :

  * Cliquer appelle `makeMove(index)`
  * Le bouton est **désactivé** si la cellule est déjà jouée ou si un gagnant est trouvé.

---

## ⚙️ Partie `<script setup>`

```js
<script setup>
import { ref, computed } from 'vue'

// 🧾 Props et événements
const props = defineProps({
  player1: Object, // { name: string, symbol: 'X' }
  player2: Object, // { name: string, symbol: 'O' }
  rounds: Number
})
const emit = defineEmits(['roundEnd']) // Émet la fin d'une manche

// 🧠 Données réactives
const board = ref(Array(9).fill(''))        // Grille du jeu (9 cases)
const currentPlayer = ref(props.player1.symbol) // Joueur courant (X ou O)
const currentRound = ref(1)                // Numéro de la manche
const moveCount = ref(0)                   // Nombre de coups joués
const winner = ref(null)                   // Symbole du gagnant ou null

// 👤 Nom du joueur courant
const currentPlayerName = computed(() => {
  return currentPlayer.value === props.player1.symbol
    ? props.player1.name
    : props.player2.name
})
```

### 💡 Que fait cette partie ?

* Utilise les `props` pour connaître les **joueurs** et le nombre de **manches**.
* Gère l’état du jeu avec des **références réactives** (`ref`) :

  * `board`: la grille du jeu (tableau de 9 cases)
  * `currentPlayer`: le joueur actuel (`'X'` ou `'O'`)
  * `winner`: contient le symbole du gagnant si la partie est gagnée
  * `moveCount`: compteur de coups pour détecter les égalités

---

## 🧠 Fonction `checkWinner`

```js
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
```

### 💡 Que fait cette fonction ?

* Vérifie toutes les combinaisons gagnantes (lignes, colonnes, diagonales).
* Si 3 cases alignées ont le **même symbole**, alors le joueur gagne → on retourne `true`.

---

## 🕹️ Fonction `makeMove`

```js
function makeMove(index) {
  if (board.value[index] !== '' || winner.value) return // Case déjà jouée ou jeu terminé
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

  switchPlayer() // Pas de gagnant ni d’égalité → on change de joueur
}
```

### 💡 Que fait cette fonction ?

* Joue le symbole du joueur actuel dans la case sélectionnée.
* Incrémente le nombre de coups joués.
* Vérifie :

  * si le joueur a gagné → émet un événement `roundEnd` avec le gagnant
  * si toutes les cases sont remplies → émet une égalité (`draw`)
  * sinon → passe au joueur suivant

---

## 🔁 Fonction `switchPlayer`

```js
function switchPlayer() {
  currentPlayer.value =
    currentPlayer.value === props.player1.symbol
      ? props.player2.symbol
      : props.player1.symbol
}
```

### 💡 Que fait cette fonction ?

* Inverse le joueur courant :

  * Si c’était `X`, ça devient `O`
  * Si c’était `O`, ça devient `X`

---




PlayerSetup.vue

Voici une **explication complète** de ce composant Vue 3 `<script setup>` et `<template>` :
Il s’agit d’un **formulaire de démarrage** pour un jeu de morpion (ou autre jeu à deux joueurs) — il permet de :

* Entrer les **noms des joueurs**
* Choisir leur **symbole**
* Indiquer le **nombre de manches**
* Et démarrer le jeu via un événement `startGame`.

---

## 🎯 Partie `<script setup>`

```js
<script setup>
import { reactive, ref } from 'vue'

// Joueur 1 avec son nom et symbole (réactif)
const p1 = reactive({
    name: '',
    symbol: '❌'
})

// Joueur 2 avec son nom et symbole (réactif)
const p2 = reactive({
    name: '',
    symbol: '⭕'
})

// Nombre de manches à jouer (par défaut : 1)
const rounds = ref(1)

// Déclaration de l'événement émis vers le parent
const emit = defineEmits(['startGame'])

// Fonction appelée au clic sur le bouton "Démarrer"
function handleSubmit() {
    if (p1.symbol === p2.symbol) {
        alert("Chaque joueur doit avoir un symbole différent.")
        return
    }

    // Émet l'événement avec les infos des joueurs et le nombre de manches
    emit('startGame', { ...p1 }, { ...p2 }, rounds.value)
}
</script>
```

### 🧠 Explications :

* `reactive` rend les objets `p1` et `p2` réactifs. On y stocke :

  * le nom du joueur
  * le symbole choisi (par défaut ❌ pour p1, ⭕ pour p2)
* `rounds` est une référence numérique pour le nombre de manches.
* `defineEmits(['startGame'])` permet d’émettre l’événement `startGame` vers le composant parent.
* `handleSubmit()` est appelée lors de la soumission du formulaire :

  * Elle empêche que les deux joueurs aient le **même symbole**
  * Puis elle émet `startGame` avec les données des deux joueurs et du nombre de manches

---

## 🖼️ Partie `<template>`

```html
<template>
  <form @submit.prevent="handleSubmit" class="space-y-4">
```

* Le formulaire appelle `handleSubmit` sans rechargement de page (`@submit.prevent`)

---

### 🧍‍♂️ Nom et symbole du joueur 1

```html
    <div>
        <label class="block font-medium">Nom Joueur 1</label>
        <input v-model="p1.name" required ... />
    </div>
    <div>
        <label class="block font-medium">Symbole Joueur 1</label>
        <select v-model="p1.symbol" ...>
            <option>❌</option>
            <option>🔥</option>
            <option>💎</option>
            <option>🍕</option>
        </select>
    </div>
```

* Le nom est obligatoire (`required`)
* Le symbole est sélectionné depuis une liste d’émojis sympas

---

### 🧍‍♀️ Nom et symbole du joueur 2

```html
    <div>
        <label class="block font-medium">Nom Joueur 2</label>
        <input v-model="p2.name" required ... />
    </div>
    <div>
        <label class="block font-medium">Symbole Joueur 2</label>
        <select v-model="p2.symbol" ...>
            <option>⭕</option>
            <option>🌟</option>
            <option>🍀</option>
            <option>🎯</option>
        </select>
    </div>
```

* Idem que le joueur 1, mais avec d'autres symboles.

---

### 🔁 Nombre de manches

```html
    <div>
        <label class="block font-medium">Nombre de manches</label>
        <input type="number" v-model.number="rounds" min="1" ... />
    </div>
```

* Champ pour saisir combien de **manches** jouer
* `v-model.number` convertit directement l’entrée en `Number`
* `min="1"` empêche de saisir une valeur invalide

---

### ▶️ Bouton de démarrage

```html
    <button type="submit" class="w-full bg-blue-600 text-white py-2 rounded">Démarrer</button>
```

* Envoie le formulaire
* Déclenche `handleSubmit`

---

## ✅ Résumé du comportement

Ce composant est l’**écran d’accueil** ou de **préparation** du jeu :

1. L’utilisateur saisit les **infos des 2 joueurs**
2. Choisit leur **symbole** et le **nombre de manches**
3. Clique sur **Démarrer**
4. Le composant **valide** les données et **émet `startGame(...)`**
5. Le **composant parent** peut alors démarrer la partie (par exemple, afficher la grille)

---




ResultModal

Bien sûr ! Voici une explication détaillée de ce composant Vue 3, qui est une **fenêtre modale de fin de manche** pour ton jeu de morpion.

---

## 🖼️ Partie `<template>`

```html
<div class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50">
  <div class="bg-white p-6 rounded-xl w-full max-w-md text-center shadow-lg">

    <!-- Titre dynamique selon résultat -->
    <h2 class="text-2xl font-bold mb-4">
      {{ result.result === 'win' ? `${result.winner} a gagné ! 🏆` : "Match nul 🤝" }}
    </h2>

    <!-- Nombre de coups si victoire -->
    <p v-if="result.result === 'win'" class="mb-2">
      Victoire en {{ result.moves }} coups
    </p>

    <!-- Affichage de la grille finale -->
    <div class="grid grid-cols-3 gap-2 my-4">
      <div
        v-for="(cell, index) in result.board"
        :key="index"
        class="h-16 w-16 flex items-center justify-center border text-2xl font-bold rounded-md bg-gray-50"
      >
        {{ cell }}
      </div>
    </div>

    <!-- Bouton pour passer à la manche suivante -->
    <button class="bg-blue-600 text-white px-4 py-2 rounded mt-4" @click="$emit('next')">
      Manche suivante
    </button>
  </div>
</div>
```

### Que fait ce template ?

* C’est une **modale** centrée et en superposition (`fixed inset-0 bg-black bg-opacity-50` pour un fond semi-transparent sombre).
* Affiche un message dynamique selon que la manche s’est terminée par une victoire ou un match nul.
* Si victoire, affiche le nombre de coups joués.
* Affiche la grille finale (9 cases) avec les symboles présents sur la grille à la fin de la manche.
* Bouton "Manche suivante" qui émet un événement `next` au parent.

---

## ⚙️ Partie `<script setup>`

```js
<script setup>
defineProps({
  result: Object // objet reçu du parent avec info sur la manche (gagnant, moves, grille)
})
defineEmits(['next']) // émet l'événement 'next' quand on veut passer à la manche suivante
</script>
```

### Que fait ce script ?

* Reçoit une `prop` `result` contenant les infos sur la fin de la manche, par exemple :

```js
{
  result: 'win' | 'draw',
  winner: "Nom du joueur",
  moves: nombreDeCoups,
  board: ['X', 'O', '', '', ...] // état final de la grille
}
```

* Permet d’émettre un événement `next` quand l’utilisateur clique sur le bouton pour signaler qu’on veut passer à la manche suivante.

---

## ✅ Résumé

Ce composant est une **fenêtre modale** qui s’affiche à la fin d’une manche pour :

* Afficher le résultat : victoire (avec le nom du gagnant et nombre de coups) ou match nul.
* Afficher la grille finale pour voir la position des symboles.
* Proposer un bouton pour continuer vers la manche suivante (ou relancer une nouvelle manche).

---


