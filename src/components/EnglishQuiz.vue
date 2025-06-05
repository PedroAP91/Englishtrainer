<template>
  <div
    class="min-h-screen flex flex-col items-center justify-center bg-gray-100 dark:bg-gray-900 font-sans"
  >
    <div
      class="w-full max-w-md bg-white dark:bg-gray-800 shadow-md rounded-lg p-4 text-center"
    >
      <transition name="fade" mode="out-in">
        <div v-if="!currentSentence" key="start">
          <button
            class="mt-4 px-4 py-2 rounded-md text-white bg-green-500 hover:bg-green-600 focus:outline-none focus:ring"
            @click="startQuiz"
            aria-label="Iniciar el quiz"
          >
            Iniciar
          </button>
        </div>
        <div v-else :key="currentSentence.spanish">
          <p>Traduce al inglés: "{{ currentSentence.spanish }}"</p>
          <label class="sr-only" for="answer">Respuesta en inglés</label>
          <input
            id="answer"
            v-model="userAnswer"
            :disabled="answerChecked"
            @keyup.enter="!answerChecked && checkAnswer"
            class="mt-4 w-full border rounded-md p-2 dark:bg-gray-700 dark:border-gray-500"
          />
          <button
            v-if="!answerChecked"
            class="mt-4 px-4 py-2 rounded-md text-white bg-blue-500 hover:bg-blue-600 focus:outline-none focus:ring"
            @click="checkAnswer"
            aria-label="Comprobar respuesta"
          >
            Comprobar
          </button>
          <transition name="fade">
            <p v-if="message" :class="feedbackClass">{{ message }}</p>
          </transition>
          <button
            v-if="answerChecked"
            class="mt-4 px-4 py-2 rounded-md text-white bg-green-500 hover:bg-green-600 focus:outline-none focus:ring"
            @click="nextSentence"
            aria-label="Cargar siguiente frase"
          >
            Siguiente frase
          </button>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: 'EnglishQuiz',
  data() {
    return {
      sentences: [
        { english: 'Hello, how are you?', spanish: 'Hola, ¿cómo estás?' },
        { english: 'Good morning', spanish: 'Buenos días' },
        { english: 'I like to learn languages', spanish: 'Me gusta aprender idiomas' },
        { english: 'This is a pen', spanish: 'Esto es un bolígrafo' }
      ],
      currentSentence: null,
      userAnswer: '',
      message: '',
      answerChecked: false,
      isCorrect: null
    }
  },
  computed: {
    feedbackClass() {
      if (this.isCorrect === null) return ''
      const base = ['mt-4', 'text-lg', 'font-bold', 'p-2', 'rounded-md']
      const color = this.isCorrect
        ? 'bg-green-100 text-green-700 dark:bg-green-900 dark:text-green-200'
        : 'bg-red-100 text-red-700 dark:bg-red-900 dark:text-red-200'
      return [...base, color]
    }
  },
  methods: {
    getRandomSentence() {
      const index = Math.floor(Math.random() * this.sentences.length)
      return this.sentences[index]
    },
    startQuiz() {
      this.currentSentence = this.getRandomSentence()
      this.userAnswer = ''
      this.message = ''
      this.answerChecked = false
      this.isCorrect = null
    },
    checkAnswer() {
      if (!this.currentSentence) return
      const user = this.userAnswer.trim().toLowerCase()
      const correct = this.currentSentence.english.toLowerCase()
      if (user === correct) {
        this.message = '¡Correcto!'
        this.isCorrect = true
      } else {
        this.message = `Inténtalo de nuevo. La frase correcta es: "${this.currentSentence.english}"`
        this.isCorrect = false
      }
      this.answerChecked = true
    },
    nextSentence() {
      this.startQuiz()
    }
  }
}
</script>

<style scoped>
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  border: 0;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>

