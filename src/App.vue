<template>
  <div class="game-container">
    <h1>El Rosco - Pasapalabra</h1>
    
    <div class="rosco-container">
      <div class="center-content" v-if="!gameOver">
        <div class="current-letter">{{ currentQuestion.char }}</div>
        <p class="question-text">{{ currentQuestion.question }}</p>
        
        <div class="input-area">
          <input 
            v-model="userAnswer" 
            @keyup.enter="checkAnswer"
            type="text" 
            placeholder="Escribe tu respuesta..." 
            autofocus
          />
          <button @click="checkAnswer">Responder</button>
        </div>
      </div>
      
      <div class="center-content" v-else>
        <h2>¡Juego Terminado!</h2>
        <p>Aciertos: <span class="score-green">{{ getScore('acierto') }}</span></p>
        <p>Fallos: <span class="score-red">{{ getScore('fallo') }}</span></p>
      </div>
      
      <div
        v-for="(item, index) in questions"
        :key="index"
        class="letter-circle"
        :class="item.status"
        :style="getLetterStyle(index)"
      >
        {{ item.char }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const questions = ref([
  { char: 'A', question: 'Empieza por A: Medio de transporte aéreo.', answer: 'avion', status: 'pending' },
  { char: 'B', question: 'Empieza por B: Vehículo ecológico de dos ruedas.', answer: 'bicicleta', status: 'pending' },
  { char: 'C', question: 'Empieza por C: Edificio donde vive la gente.', answer: 'casa', status: 'pending' },
  { char: 'D', question: 'Empieza por D: Herramienta para aflojar tornillos.', answer: 'destornillador', status: 'pending' },
  { char: 'E', question: 'Empieza por E: Animal enorme con trompa.', answer: 'elefante', status: 'pending' }
]);

const currentIndex = ref(0);
const userAnswer = ref('');

const currentQuestion = computed(() => questions.value[currentIndex.value]);
const gameOver = computed(() => !questions.value.some(q => q.status === 'pending'));

const checkAnswer = () => {
  if (gameOver.value) return;

  const cleanAnswer = userAnswer.value.toLowerCase().trim();
  const correctAnswer = questions.value[currentIndex.value].answer.toLowerCase();

  if (cleanAnswer === correctAnswer) {
    questions.value[currentIndex.value].status = 'acierto';
  } else {
    questions.value[currentIndex.value].status = 'fallo';
  }

  userAnswer.value = '';
  nextQuestion();
};

const nextQuestion = () => {
  let next = currentIndex.value + 1;
  while (next < questions.value.length && questions.value[next].status !== 'pending') {
    next++;
  }
  if (next < questions.value.length) {
    currentIndex.value = next;
  }
};

const getScore = (status) => {
  return questions.value.filter(q => q.status === status).length;
};

const getLetterStyle = (index) => {
  const totalLetters = questions.value.length;
  const angle = (index / totalLetters) * 360 - 90; 
  const radius = 180;

  const radian = angle * (Math.PI / 180);
  const x = Math.cos(radian) * radius;
  const y = Math.sin(radian) * radius;

  return {
    transform: `translate(${x}px, ${y}px)`
  };
};
</script>

<style scoped>
.game-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background-color: #f0f8ff;
}

.rosco-container {
  position: relative;
  width: 450px;
  height: 450px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: white;
  border-radius: 50%;
  box-shadow: 0 10px 25px rgba(0,0,0,0.1);
}

.center-content {
  text-align: center;
  width: 60%;
  z-index: 10;
}

.current-letter {
  font-size: 3rem;
  font-weight: bold;
  color: #007bff;
  margin-bottom: 10px;
}

.question-text {
  font-size: 1.1rem;
  color: #333;
  margin-bottom: 20px;
}

.input-area {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

input {
  padding: 10px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
  text-align: center;
}

button {
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-weight: bold;
}

button:hover {
  background-color: #0056b3;
}

.score-green { color: #28a745; font-weight: bold; }
.score-red { color: #dc3545; font-weight: bold; }

.letter-circle {
  position: absolute;
  width: 45px;
  height: 45px;
  border-radius: 50%;
  background-color: #007bff;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: bold;
  font-size: 1.2rem;
  border: 3px solid #0056b3;
  box-shadow: 0 4px 8px rgba(0,0,0,0.2);
  transition: background-color 0.3s ease;
}

.letter-circle.acierto {
  background-color: #28a745;
  border-color: #1e7e34;
}

.letter-circle.fallo {
  background-color: #dc3545;
  border-color: #bd2130;
}
</style>