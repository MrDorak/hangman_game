<template>
  <div class="home">
    <button v-show="!ready" @click="findWord">Commencer</button>
    {{ word }}
    
    <Game v-if=ready
      :hidden_word=word
    />
  </div>
</template>

<script>
import Game from '@/components/Game.vue'
import faker from "faker"

export default {
  name: 'Home',
  components: {
    Game
  },
  data: () => ({
    word: "AZ",
    ready: false,
  }),
  mounted() {
  },
  methods: {
    findWord() {
      let word = faker.random.word();
      while(word.length < 4) {
        word = faker.random.word();
      }
      
      this.word = word.replace(/\W/g, '').toUpperCase();
      this.ready = true
    }
  }
}
</script>
