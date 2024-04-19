<script setup lang="ts">
  import GameHeader from './components/GameHeader.vue';
  import GameFigure from './components/GameFigure.vue';
  import GameNotification from './components/GameNotification.vue';
  import GamePopup from './components/GamePopup.vue';
  import GameWord from './components/GameWord.vue';
  import GameWrongLetters from './components/GameWrongLetters.vue';
  import { computed, ref, watch } from 'vue';

  const word = ref('Аружан')
  const letters = ref<string[]>([])
  const correctLetters = computed(()=> letters.value.filter(x=> word.value.includes(x)))
  const wrongLetters = computed(()=> letters.value.filter(x=> !word.value.includes(x)))
  const notification = ref<InstanceType<typeof GameNotification> | null>(null)
  const popup = ref<InstanceType<typeof GamePopup> | null>(null)

  watch(wrongLetters, ()=> {
    if(wrongLetters.value.length === 6) {
      popup.value?.open('lose')
    }
  })

  watch(correctLetters, ()=> {
    if(correctLetters.value.length === word.value.length) {
      popup.value?.open('win')
    }
  })

  window.addEventListener('keydown', ({key})=> {
    if(letters.value.includes(key)) {
      notification.value?.open()
      setTimeout(()=> notification.value?.close(), 2000)
      return
    }

    if(/[а-яА-ЯёЁ]/.test(key)) {
      letters.value.push(key.toLowerCase())
    }
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

  <GamePopup ref="popup" :word="word"/>
  <GameNotification ref="notification"/>
</template>