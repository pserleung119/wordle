<script>
import { words } from '../data/words.js'

export default {
  data() {
    return {
      rows: new Array(6).fill(new Array(5).fill('')),
      word: words[Math.floor(Math.random() * words.length)],
      input: '',
      finished: false
    }
  },
  computed: {
    gameResult() {
      if (this.rows.every(word => word[0] != '')) {
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
        const targetRowIndex = this.rows.findIndex(row => row[0] === '')

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
  <div>
    <h1>Wordle</h1>
    <div>
      <p v-for="word in rows" class="word-row">
        <div v-for="(char, index) in word" class="word-frame">
          <span :class="charClass(char, index)">{{ char }}</span>
        </div>
      </p>
    </div>
    <input type="text" v-model="input" maxlength="5" @keyup="keyUpAction" :disabled="finished">
    <h5 v-if="finished">{{ gameResult }}</h5>
  </div>
</template>

<style scoped>
a {
  color: #42b983;
}

li {
  list-style: none;
}

.word-row {
  display: flex;
  justify-content: center;
}
.word-frame {
  border: 1px solid #888888;
  border-radius: 0.2rem;
  width: 30px;
  height: 35px;
  margin: 0 0.2rem;
  display: flex;
  justify-content: center;
  align-items: center;
}

span {
  font-size: 24px;
}

.text-black {
  color: #888888;
}
.text-yellow {
  color: rgb(206, 206, 0);
}
.text-green {
  color: #42b983;
}

input {
  height: 24px;
  text-transform: uppercase;
}
</style>
