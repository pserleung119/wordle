<script>
import { words } from '../data/words.js'

export default {
  data() {
    return {
      rows: [],
      word: '',
      input: '',
      finished: false
    }
  },
  mounted() {
    this.initializeSession()
  },
  computed: {
    gameResult() {
      if (this.rows.every(word => word != '')) {
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
        const targetRowIndex = this.rows.findIndex(row => row === '')

        if (targetRowIndex == 5 || input == this.word) {
          this.finished = true
          this.setFinishFlag(true)
        }

        this.rows[targetRowIndex] = input
        this.setAttemptHistory(input, targetRowIndex)
        this.input = ''
      }
    },
    charStyle(char, index) {
      let klass = 'text-black'
      if (this.word.indexOf(char) != -1) {
        klass = 'text-yellow'
      }
      if (this.word[index] == char) {
        klass = 'text-green'
      }
      return klass
    },
    initializeSession() {
      const date = new Date()

      if (!localStorage.getItem('word') || new Date(JSON.parse(localStorage.getItem('word'))['expiredAt']) < date) {
        const word = words[Math.floor(Math.random() * words.length)]
        localStorage.setItem('word', JSON.stringify({
          word: word,
          expiredAt: date.setDate(date.getDate() + 1),
          attemptHistory: new Array(6).fill('')
        }))
      }
      this.word = JSON.parse(localStorage.getItem('word'))['word']
      this.rows = JSON.parse(localStorage.getItem('word'))['attemptHistory']
      this.finished = JSON.parse(localStorage.getItem('word'))['finished']
    },
    setAttemptHistory(attempt, attemptIndex) {
      const session = JSON.parse(localStorage.getItem('word'))
      session['attemptHistory'][attemptIndex] = attempt
      localStorage.setItem('word', JSON.stringify(session))
    },
    setFinishFlag(flag) {
      const session = JSON.parse(localStorage.getItem('word'))
      session['finished'] = flag
      localStorage.setItem('word', JSON.stringify(session))
    }
  }
}
</script>

<template>
  <div>
    <h1>Wordle</h1>
    <div>
      <p v-for="word in rows" class="word-row">
        <div v-for="(char, index) in 5" class="word-frame">
          <span :class="charStyle(word[index], index)">{{ word[index] || '' }}</span>
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
