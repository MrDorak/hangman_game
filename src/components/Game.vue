<template>
  <div>
    <canvas
      width="250"
      height="250"
      ref="canvas"
      style="border: 1px solid black"
    >
      Your browser does not support the HTML 5 Canvas.
    </canvas>
    <div style="margin: 0 0 15px">
     <span v-html="answerProgress"></span> | Failed: {{ game.fail }} time
    </div>

    <div style="margin: 0 0 15px 0">
        <div v-if="canPlay && !game.won">
          <button 
            v-for="(alpha, i) in alphabet"
            :key="i"
            @click="isLetterInWord(alpha)"
          >
            {{ alpha }}
          </button>
        </div>
        <div v-else-if="!canPlay && !game.won">
          Uh-oh, looks like you have lost... better luck next time!
        </div>
        <div v-else>
          Hurray ! You WON ! 
        </div>
    </div>		

    <button @click="() => $router.go()">Choose a new word</button>
    <button @click="softResetGame()" v-if="!canPlay">Restart with the same word</button>
  </div>
</template>

<script>
import { defineComponent } from "vue";

let initGameData = () => ({
    fail: 0,
    used_letters: [],
    right_letters: [],
    won: false,
})

export default defineComponent({
  name: "Game",
  props: ["hidden_word"],
  data: () => ({
    canvas: null,
    context: null,
    totalParts: [
      "gallows",
      "head",
      "body",
      "rightArm",
      "leftArm",
      "rightLeg",
      "leftLeg",
      "dead",
    ],
		game: initGameData()
  }),
  watch: {
    answerProgress(newValue) {
			if(newValue === this.hidden_word) {
        this.game.won = true;
			}
    },
  },
  mounted() {
    window.addEventListener("keypress", e => {
      this.isLetterInWord(String.fromCharCode(e.keyCode).toUpperCase())
    });
    this.initCanva();
  },
  methods: {
    initCanva() {
      this.canvas = this.$refs.canvas;
      this.context = this.canvas.getContext("2d");
    },
    hardResetGame(){
      this.$router.go();
    },
    softResetGame() {
			this.context.clearRect(0, 0, this.canvas.width, this.canvas.height);
      Object.assign(this.$data.game, initGameData());
    },
    isLetterInWord(letter) {
      if(this.game.won === true || !this.canPlay) return

      if (this.hidden_word.includes(letter) && this.game.used_letters.indexOf(letter) === -1) {
        this.game.used_letters.push(letter);
        this.game.right_letters.push(letter);
      } else {
				this.draw(this.totalParts[this.game.fail++]);
				this.game.used_letters = this.addToArrayIfNotExists(this.game.used_letters, letter)
      }
    },
    draw(part) {
      switch (part) {
        case "gallows":
          this.context.strokeStyle = "#444";
          this.context.lineWidth = 10;
          this.context.beginPath();
          this.context.moveTo(175, 235);
          this.context.lineTo(5, 235);
          this.context.moveTo(30, 235);
          this.context.lineTo(30, 15);
          this.context.lineTo(150, 15);
          this.context.lineTo(150, 35);
          this.context.moveTo(30, 65);
          this.context.lineTo(60, 15);
          this.context.stroke();
          break;

        case "head":
          this.context.lineWidth = 5;
          this.context.beginPath();
          this.context.arc(150, 55, 20, 0, Math.PI * 2, true);
          this.context.closePath();
          this.context.stroke();
          break;

        case "body":
          this.context.beginPath();
          this.context.moveTo(150, 75);
          this.context.lineTo(150, 140);
          this.context.stroke();
          break;

        case "rightArm":
          this.context.beginPath();
          this.context.moveTo(150, 85);
          this.context.lineTo(135, 125);
          this.context.stroke();
          break;

        case "leftArm":
          this.context.beginPath();
          this.context.moveTo(150, 85);
          this.context.lineTo(165, 125);
          this.context.stroke();
          break;

        case "rightLeg":
          this.context.beginPath();
          this.context.moveTo(150, 140);
          this.context.lineTo(140, 200);
          this.context.stroke();
          break;

        case "leftLeg":
          this.context.beginPath();
          this.context.moveTo(150, 140);
          this.context.lineTo(165, 200);
          this.context.stroke();
          break;
      }
		},
		addToArrayIfNotExists(array, val) {
			return array.filter(a => a !== val).concat([val]);
		}
  },
  computed: {
		alphabet: () => "abcdefghijklmnopqrstuvwxyz".split("").map(c => c.toUpperCase()),
		canPlay() {
			return this.totalParts[this.game.fail] !== 'dead';
		},
    answerProgress() {
			return this.hidden_word
			.split("")
			.map((l, idx) => {
				if(idx === 0 || idx === this.hidden_word.length - 1) { // show the first and last letter of the word, and every other same letters as these
					this.game.right_letters = this.addToArrayIfNotExists(this.game.right_letters, l)
					this.game.used_letters = this.addToArrayIfNotExists(this.game.used_letters, l)
				}
				if (l === ' ') {
					return '&nbsp;'
				}
				return this.game.right_letters.indexOf(l) !== -1 ? l : " _ "
			})
			.join("");
    },
  },
});
</script>