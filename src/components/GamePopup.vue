<script setup lang="ts">
  import { ref } from 'vue';
  import GameStatus from '../types/GameStatus'

  interface Props{
    word: string
  }
  defineProps<Props>()

  const gameStatus = ref<GameStatus | null>(null)
  const isVisable = ref(false)

  const open = (status:GameStatus)=> {
    gameStatus.value = status
    isVisable.value = true
  }

  const close = () => {
    isVisable.value = false
  }

  defineExpose({
    open,
    close
  })

  const emit =defineEmits<{
    (e: 'restart'): void
  }>()
</script>
<template>
    <div v-show="isVisable" class="popup-container">
    <div class="popup">
      <h2 v-if="gameStatus === 'win'">Поздравляю, вы победили! 😃</h2>
      <template v-else>
        <h2>Вы проиграли. 😕</h2>
        <h3>...имя: {{ word }}</h3>
      </template>
      <button @click="emit('restart')">Сыграть еще раз</button>
    </div>
  </div>
</template>