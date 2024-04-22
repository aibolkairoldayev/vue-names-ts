<script setup lang="ts">
  import GameHeader from './components/GameHeader.vue';
  import GameFigure from './components/GameFigure.vue';
  import GamePopup from './components/GamePopup.vue';
  import GameWord from './components/GameWord.vue';
  import GameWrongLetters from './components/GameWrongLetters.vue';
  import { computed, ref, watch } from 'vue';
  import { useRandomWord } from './composables/useRandomWord';
  import { useLetters } from './composables/useLetters';
  import {useNotification} from './composables/useNotification'
  
  
  const popup = ref<InstanceType<typeof GamePopup> | null>(null)
  
  const {word, getRandomWord} = useRandomWord()
  const {letters, correctLetters, wrongLetters, isLose, isWin, addLetter, resetLetter} = useLetters(word)
  const {notification, showNotification} = useNotification()
  const restart = async()=> {
    await getRandomWord()
    resetLetter()
    popup.value?.close()
  }

  watch(wrongLetters, ()=> {
    if(isLose.value) {
      popup.value?.open('lose')
    }
  })

  watch(correctLetters, ()=> {
    if(isWin.value) {
      popup.value?.open('win')
    }
  })

  window.addEventListener('keydown', ({key})=> {
    if(isLose.value || isWin.value) {
      return
    }
    if(letters.value.includes(key)) {
      showNotification()
    }

    addLetter(key)
  })
</script>
<template>
  <GameHeader/>
  <div class="game-container">
    <GameFigure :wrong-letters-count="wrongLetters.length"/>

    <div class="wrong-letters-container">
      <GameWrongLetters :wrong-letters="wrongLetters"/>
    </div>
    <GameWord :word="word" :correct-letters="correctLetters"/>
  </div>

  <GamePopup ref="popup" :word="word" @click="restart"/>
  <GameNotification ref="notification"/>
</template>