<template>
  <div class="quiz-wrapper">
    <div class="quiz-container">
      <transition name="fade" mode="out-in">
        <div v-if="!currentSentence" key="start">
          <button
            class="primary-btn"
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
          />
          <button
            v-if="!answerChecked"
            class="primary-btn"
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
            class="primary-btn"
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
      return this.isCorrect ? ['feedback', 'correct'] : ['feedback', 'incorrect']
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
.quiz-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-color: #f2f2f2;
  font-family: 'Segoe UI', Roboto, 'Open Sans', sans-serif;
}

.quiz-container {
  background: #ffffff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  text-align: center;
  width: 100%;
  max-width: 400px;
}

.primary-btn {
  padding: 10px 20px;
  border: none;
  border-radius: 6px;
  background-color: #42b983;
  color: #fff;
  cursor: pointer;
  font-size: 1em;
  margin-top: 10px;
}
.primary-btn:hover {
  background-color: #36966f;
}

input,
select {
  padding: 8px 12px;
  margin-top: 10px;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 1em;
  width: 100%;
  box-sizing: border-box;
}

.feedback {
  margin-top: 15px;
  font-size: 1.2em;
  padding: 10px;
  border-radius: 6px;
  font-weight: bold;
}

.correct {
  background-color: #d4edda;
  color: #155724;
}

.incorrect {
  background-color: #f8d7da;
  color: #721c24;
}

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

@media (prefers-color-scheme: dark) {
  .quiz-wrapper {
    background-color: #121212;
    color: #ffffff;
  }
  .quiz-container {
    background: #1e1e1e;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.6);
  }
  .primary-btn {
    background-color: #3a8f78;
  }
  .primary-btn:hover {
    background-color: #2c6c59;
  }
  input,
  select {
    background-color: #2c2c2c;
    border-color: #555;
    color: #ffffff;
  }
  .correct {
    background-color: #225533;
    color: #d5f5e3;
  }
  .incorrect {
    background-color: #5d222a;
    color: #f5d5da;
  }
}
</style>

