<script setup lang="ts">
  import GameHeader from './components/GameHeader.vue';
  import GameFigure from './components/GameFigure.vue';
  import GameNotification from './components/GameNotification.vue';
  import GamePopup from './components/GamePopup.vue';
  import GameWord from './components/GameWord.vue';
  import GameWrongLetters from './components/GameWrongLetters.vue';
  import { computed, ref } from 'vue';

  const word = ref('Аружан')
  const letters = ref<string[]>([])
  const correctLetters = computed(()=> letters.value.filter(x=> word.value.includes(x)))

  window.addEventListener('keydown', ({key})=> {
    if(/[а-яА-ЯёЁ]/.test(key)) {
      letters.value.push(key.toLowerCase())
    }
  })
</script>
<template>
  <GameHeader/>
  <div class="game-container">
    <GameFigure/>

    <div class="wrong-letters-container">
      <GameWrongLetters/>
    </div>
    <GameWord :word="word" :correct-letters="correctLetters"/>
  </div>

  <GamePopup v-if="false"/>
  <GameNotification/>
</template>