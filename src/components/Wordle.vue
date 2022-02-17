<script>
import { words } from '../data/words.js'

export default {
  data() {
    return {
      rows: new Array(6).fill([]),
      word: words[Math.floor(Math.random() * words.length)],
      input: '',
      finished: false
    }
  },
  computed: {
    gameResult() {
      if (this.rows.every(word => word.length > 0)) {
        return 'Game Over! :('
      }
      return 'Game Finished! :)'
    }
  },
  methods: {
    keyUpAction(event) {
      if (event.key == 'Enter') {
        const input = this.input.toUpperCase()
        if (input.length != 5) {
          console.log('word count not enough')
          return
        }
        if (words.indexOf(input) == -1) {
          console.log('word not in dictionary')
          return
        }
        const targetRowIndex = this.rows.findIndex(row => row.length === 0)

        if (targetRowIndex == 5 || input == this.word) {
          this.finished = true
        }

        this.rows[targetRowIndex] = input.split('')
        this.input = ''
      }
    },
    charClass(char, index) {
      let klass = 'text-black'
      if (this.word.indexOf(char) != -1) {
        klass = 'text-yellow'
      }
      if (this.word[index] == char) {
        klass = 'text-green'
      }
      return klass
    }
  }
}
</script>

<template>
  <h1>Wordle</h1>
  <div>
    <p v-for="word in rows">
      <span v-for="(char, index) in word" :class="charClass(char, index)">{{ char }}</span>
    </p>
  </div>
  <input type="text" v-model="input" maxlength="5" @keyup="keyUpAction" :disabled="finished">
  <h5 v-if="finished">{{ gameResult }}</h5>
</template>

<style scoped>
a {
  color: #42b983;
}

li {
  list-style: none;
}

span {
  margin: 0 0.5rem;
  font-size: 24px;
}

.text-black {
  color: #888888;
}
.text-yellow {
  color: rgb(206, 206, 0);
}
.text-green {
  color: rgb(42, 255, 42);
}

input {
  height: 24px;
  text-transform: uppercase;
}
</style>
