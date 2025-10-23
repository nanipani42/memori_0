<script setup>
import Card from './Card.vue';
import { ref, onMounted } from 'vue';

const cards = ref([])
const flippedCards = ref([])
const lockBoard = ref(false)
const moves = ref(0)
const score = ref(0)
const timer = ref(0)
let timerInterval = null
const level = ref(1)

const imageContext = import.meta.glob('@/assets/images/memori_*.jpg', { eager: true, import: 'default' });
const imageSymbols = Object.values(imageContext);

onMounted(() => {
    cards.value = generateCards(level.value)
})

function generateCards(level) {
    const numPairs = level * 2
    const symbols = imageSymbols

    const maxPairs = Math.min(numPairs, symbols.length)
    const selectedSymbols = symbols.slice(0, maxPairs)

    let newCards = []

    selectedSymbols.forEach((symbol, index) => {
        newCards.push({ id: index * 2 + 1, symbol, isFlipped: false, matched: false })
        newCards.push({ id: index * 2 + 2, symbol, isFlipped: false, matched: false })
    })

    newCards.sort(() => Math.random() - 0.5)

    return newCards
}

function nextLevel() {
    level.value++
    cards.value = generateCards(level.value)
    moves.value = 0
    timer.value = 0
    flippedCards.value = []
    lockBoard.value = false
}

function handleFlip(card) {
    startTimer()
    if (card.isFlipped || card.matched || lockBoard.value) return

    card.isFlipped = true
    flippedCards.value.push(card)

    if (flippedCards.value.length === 2) {
        lockBoard.value = true
        checkMatch()
    }

    moves.value++
}

function startTimer() {
    if (!timerInterval) {
        timerInterval = setInterval(() => {
            timer.value++
        }, 1000)
    }
}

function stopTimer() {
    clearInterval(timerInterval)
    timerInterval = null
}



function checkMatch() {
    const [card1, card2] = flippedCards.value

    if (card1.symbol === card2.symbol) {
        card1.matched = true
        card2.matched = true
        lockBoard.value = false
        score.value += 10
        console.log('MATCH')
    }
    else {
        setTimeout(() => {
            card1.isFlipped = false
            card2.isFlipped = false
            lockBoard.value = false
            console.log("nesto")
        }, 1000)
        console.log('NOT MATCH')
    }

    flippedCards.value = []
    checkGameOver()
}

function checkGameOver() {
    const allMatched = cards.value.every(card => card.matched)
    if (allMatched) {
        stopTimer()
        setTimeout(() => {
            nextLevel()
        }, 1000)
    }

}

</script>

<template>
    <h1>LEVEL {{ level }}</h1>
    <div>POTEZI {{ moves }}</div>
    <div>POENI {{ score }}</div>
    <div>TIMER {{ timer }}</div>
    <div class="game-board">
        <Card v-for="card in cards" :key="card.id" :card="card" @flip="handleFlip" />
    </div>

</template>

<style>
.game-board {
  display: grid;
  grid-template-columns: repeat(4, 1fr); 
  gap: 10px; 
  max-width: fit-content; 
  margin: 0 auto; 
  padding: 10px; 
}


@media (max-width: 600px) {
  .game-board {
    grid-template-columns: repeat(2, 1fr);
  }
}
</style>