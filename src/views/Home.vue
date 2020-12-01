<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <GameSettings />
  </div>
</template>

<script>
// @ is an alias to /src
import GameSettings from '@/components/GameSettings.vue'

export default {
  name: 'Home',
  components: {
    GameSettings
  }
}
</script>
