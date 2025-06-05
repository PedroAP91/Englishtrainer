<template>
  <div class="min-h-screen flex flex-col items-center justify-center bg-gray-100 dark:bg-gray-900 font-sans">
    <div class="w-full max-w-md bg-white dark:bg-gray-800 shadow-md rounded-lg p-4 text-center">
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
          <p class="mb-4">Identifica el tiempo verbal en: "{{ currentSentence.spanish }}"</p>
          <label class="sr-only" for="tense">Tiempo verbal</label>
          <select
            id="tense"
            v-model="selectedTense"
            :disabled="answerChecked"
            class="mt-2 w-full border rounded-md p-2 dark:bg-gray-700 dark:border-gray-500"
          >
            <option disabled value="">Selecciona un tiempo</option>
            <option v-for="t in tenses" :key="t.value" :value="t.value">{{ t.label }}</option>
          </select>
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
          <p v-if="answerChecked" class="mt-2 text-sm text-gray-500 dark:text-gray-400">Frase en inglés: "{{ currentSentence.english }}"</p>
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
const subjects = [
  { en: 'I', es: 'Yo' },
  { en: 'You', es: 'Tú' },
  { en: 'He', es: 'El' },
  { en: 'She', es: 'Ella' },
  { en: 'We', es: 'Nosotros' },
  { en: 'They', es: 'Ellos' }
]

const verbs = [
  {
    base: 'eat',
    forms: {
      present: { en: 'eat', es: 'como' },
      past: { en: 'ate', es: 'comí' },
      future: { en: 'will eat', es: 'comeré' }
    }
  },
  {
    base: 'go',
    forms: {
      present: { en: 'go', es: 'voy' },
      past: { en: 'went', es: 'fui' },
      future: { en: 'will go', es: 'iré' }
    }
  },
  {
    base: 'read',
    forms: {
      present: { en: 'read', es: 'leo' },
      past: { en: 'read', es: 'leí' },
      future: { en: 'will read', es: 'leeré' }
    }
  }
]

const objects = [
  { en: 'an apple', es: 'una manzana' },
  { en: 'the book', es: 'el libro' },
  { en: 'in the park', es: 'en el parque' }
]

export default {
  name: 'EnglishQuiz',
  data() {
    return {
      tenses: [
        { value: 'present', label: 'Presente' },
        { value: 'past', label: 'Pasado' },
        { value: 'future', label: 'Futuro' }
      ],
      selectedTense: '',
      currentSentence: null,
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
    tenseLabel(val) {
      const t = this.tenses.find((t) => t.value === val)
      return t ? t.label : val
    },
    generateSentence() {
      const subj = subjects[Math.floor(Math.random() * subjects.length)]
      const verb = verbs[Math.floor(Math.random() * verbs.length)]
      const obj = objects[Math.floor(Math.random() * objects.length)]
      const options = ['present', 'past', 'future']
      const tense = options[Math.floor(Math.random() * options.length)]
      const english = `${subj.en} ${verb.forms[tense].en} ${obj.en}`
      const spanish = `${subj.es} ${verb.forms[tense].es} ${obj.es}`
      this.currentSentence = { english, spanish, tense }
      this.selectedTense = ''
      this.message = ''
      this.answerChecked = false
      this.isCorrect = null
    },
    startQuiz() {
      this.generateSentence()
    },
    checkAnswer() {
      if (!this.selectedTense) {
        this.message = 'Selecciona un tiempo'
        return
      }
      this.isCorrect = this.selectedTense === this.currentSentence.tense
      this.message = this.isCorrect
        ? '¡Correcto!'
        : `Inténtalo de nuevo. El tiempo correcto es: "${this.tenseLabel(this.currentSentence.tense)}"`
      this.answerChecked = true
    },
    nextSentence() {
      this.generateSentence()
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
