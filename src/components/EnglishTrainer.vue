<template>
  <div class="trainer">
    <p v-if="sentenceWords.length">
      <span v-for="(word, index) in sentenceWords" :key="index">
        <template v-if="index === missingWordIndex">
          <select v-model="userAnswer">
            <option disabled value="">Select</option>
            <option v-for="opt in options" :key="opt">{{ opt }}</option>
          </select>
        </template>
        <template v-else>
          {{ word }}
        </template>
        <span v-if="index < sentenceWords.length - 1"> </span>
      </span>
    </p>
    <button @click="checkAnswer" :disabled="!userAnswer">Check</button>
    <p v-if="feedback">{{ feedback }}</p>
    <button @click="nextSentence">Next</button>
  </div>
</template>

<script>
export default {
  name: 'EnglishTrainer',
  data() {
    return {
      sentences: [
        'I eat apples every day.',
        'She went to the store yesterday.',
        'They are playing soccer now.'
      ],
      verbs: {
        eat: ['eat', 'eats', 'ate', 'eaten', 'eating'],
        go: ['go', 'goes', 'went', 'gone', 'going'],
        play: ['play', 'plays', 'played', 'playing']
      },
      currentSentence: '',
      sentenceWords: [],
      missingWordIndex: null,
      originalWord: '',
      options: [],
      userAnswer: '',
      feedback: ''
    }
  },
  created() {
    this.nextSentence()
  },
  methods: {
    nextSentence() {
      this.feedback = ''
      this.userAnswer = ''
      this.currentSentence = this.sentences[
        Math.floor(Math.random() * this.sentences.length)
      ]
      this.detectVerb()
    },
    detectVerb() {
      const words = this.currentSentence.split(/\s+/)
      let foundBase = ''
      let foundIndex = -1
      let foundWord = ''
      words.forEach((word, index) => {
        const clean = word.replace(/[.,!?]/g, '').toLowerCase()
        for (const base in this.verbs) {
          if (this.verbs[base].includes(clean)) {
            foundBase = base
            foundIndex = index
            foundWord = word
            break
          }
        }
      })
      this.sentenceWords = words
      if (foundIndex >= 0) {
        this.missingWordIndex = foundIndex
        this.originalWord = foundWord
        this.options = this.verbs[foundBase]
      }
    },
    checkAnswer() {
      const cleanOriginal = this.originalWord.replace(/[.,!?]/g, '').toLowerCase()
      if (this.userAnswer.toLowerCase() === cleanOriginal) {
        this.feedback = 'Correct!'
      } else {
        this.feedback = `Incorrect. The correct answer is "${this.originalWord}".`
      }
    }
  }
}
</script>

<style scoped>
.trainer {
  text-align: center;
}
select {
  margin: 0 4px;
}
</style>
