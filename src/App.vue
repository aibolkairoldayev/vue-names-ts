<script setup lang="ts">
  import GameHeader from './components/GameHeader.vue';
  import GameFigure from './components/GameFigure.vue';
  import GameNotification from './components/GameNotification.vue';
  import GamePopup from './components/GamePopup.vue';
  import GameWord from './components/GameWord.vue';
  import GameWrongLetters from './components/GameWrongLetters.vue';
  import { computed, ref, watch } from 'vue';
  import axios from 'axios'

  const word = ref('')
  const letters = ref<string[]>([])
  const correctLetters = computed(()=> letters.value.filter(x=> word.value.includes(x)))
  const wrongLetters = computed(()=> letters.value.filter(x=> !word.value.includes(x)))
  const isLose = computed(()=>wrongLetters.value.length === 6)
  const isWin = computed(()=>[...word.value].every(x=> correctLetters.value.includes(x)))
  const notification = ref<InstanceType<typeof GameNotification> | null>(null)
  const popup = ref<InstanceType<typeof GamePopup> | null>(null)
  const getRundomWord = async ()=>{
    try {
      const {data} = await axios<{FirstName: string}>(
        'https://api.randomdatatools.ru/?unescaped=false&params=FirstName'
      )
      console.log(data.FirstName)
      word.value = data.FirstName.toLowerCase()
      console.log(data.FirstName)
    } catch(err) {
      console.log(err)
      word.value = ''
    }
  }
  getRundomWord()

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
      notification.value?.open()
      setTimeout(()=> notification.value?.close(), 2000)
      return
    }

    if(/[а-яА-ЯёЁ]/.test(key)) {
      letters.value.push(key.toLowerCase())
    }
  })

  const restart = async()=> {
    await getRundomWord()
    letters.value = []
    popup.value?.close()
  }
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