<script setup>
import { defineProps,defineEmits, ref } from 'vue';

const props = defineProps({
    card: Object
})
const emit = defineEmits(['flip'])

function flipCard() {
    emit('flip', props.card)
}
</script>

<template>
    <div @click="flipCard" class="card">
        <div v-if="props.card.isFlipped" class="front">
           <img :src="props.card.symbol" alt="Card Symbol" class="card-image" />
        </div>      
        <div v-else class="back">?</div>
    </div>
</template>

<style scoped>

.card {
  width: 100px;
  height: 100px;
  border: 2px solid #333;
}
.front, .back {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 24px;
}

img {
    width: 100%; /* or any custom size */
    height: 100%; 
    object-fit: contain;
}
</style>
