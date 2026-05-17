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
          <div class="button-group">
            <button @click="checkAnswer" class="btn-responder">Responder</button>
            <button @click="pasapalabra" class="btn-pasapalabra">Pasapalabra</button>
          </div>
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
  { char: 'E', question: 'Empieza por E: Animal enorme con trompa.', answer: 'elefante', status: 'pending' },
  { char: 'F', question: 'Empieza por F: Animal marino juguetón que aplaude con sus aletas.', answer: 'foca', status: 'pending' },
  { char: 'G', question: 'Empieza por G: Felino doméstico que maúlla.', answer: 'gato', status: 'pending' },
  { char: 'H', question: 'Empieza por H: Agua congelada.', answer: 'hielo', status: 'pending' },
  { char: 'I', question: 'Empieza por I: Porción de tierra rodeada de agua por todas partes.', answer: 'isla', status: 'pending' },
  { char: 'J', question: 'Empieza por J: Animal africano de cuello muy largo.', answer: 'jirafa', status: 'pending' },
  { char: 'K', question: 'Empieza por K: Fruta de piel marrón peluda y pulpa verde.', answer: 'kiwi', status: 'pending' },
  { char: 'L', question: 'Empieza por L: Satélite natural de la Tierra.', answer: 'luna', status: 'pending' },
  { char: 'M', question: 'Empieza por M: Fruta roja o verde que le cayó en la cabeza a Newton.', answer: 'manzana', status: 'pending' },
  { char: 'N', question: 'Empieza por N: Masa de vapor de agua que flota en el cielo.', answer: 'nube', status: 'pending' },
  { char: 'Ñ', question: 'Empieza por Ñ: Ave corredora similar al avestruz que habita en Sudamérica.', answer: 'ñandu', status: 'pending' },
  { char: 'O', question: 'Empieza por O: Mamífero carnívoro al que le gusta mucho la miel.', answer: 'oso', status: 'pending' },
  { char: 'P', question: 'Empieza por P: Animal considerado el mejor amigo del ser humano.', answer: 'perro', status: 'pending' },
  { char: 'Q', question: 'Empieza por Q: Alimento sólido derivado de la leche.', answer: 'queso', status: 'pending' },
  { char: 'R', question: 'Empieza por R: Roedor pequeño al que le gusta el queso.', answer: 'raton', status: 'pending' },
  { char: 'S', question: 'Empieza por S: Estrella luminosa, centro de nuestro sistema planetario.', answer: 'sol', status: 'pending' },
  { char: 'T', question: 'Empieza por T: Medio de transporte formado por vagones.', answer: 'tren', status: 'pending' },
  { char: 'U', question: 'Empieza por U: Fruta con la que se elabora el vino.', answer: 'uva', status: 'pending' },
  { char: 'V', question: 'Empieza por V: Animal de granja que nos da leche.', answer: 'vaca', status: 'pending' },
  { char: 'W', question: 'Empieza por W: Tecnología que nos permite conectarnos a Internet sin cables.', answer: 'wifi', status: 'pending' },
  { char: 'X', question: 'Empieza por X: Instrumento musical de percusión con láminas de madera.', answer: 'xilofono', status: 'pending' },
  { char: 'Y', question: 'Empieza por Y: Embarcación de lujo para recreo.', answer: 'yate', status: 'pending' },
  { char: 'Z', question: 'Empieza por Z: Prenda de calzado que cubre el pie.', answer: 'zapato', status: 'pending' }
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

const pasapalabra = () => {
  if (gameOver.value) return;
  userAnswer.value = '';
  nextQuestion();
};

const nextQuestion = () => {
  let next = currentIndex.value + 1;
  
  if (next >= questions.value.length) {
    next = 0;
  }
  
  let found = false;
  let attempts = 0;
  
  while (!found && attempts < questions.value.length) {
    if (questions.value[next].status === 'pending') {
      found = true;
    } else {
      next++;
      if (next >= questions.value.length) next = 0;
    }
    attempts++;
  }
  
  if (found) {
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

.button-group {
  display: flex;
  gap: 10px;
  justify-content: center;
}

button {
  padding: 10px 15px;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-weight: bold;
}

.btn-responder { background-color: #007bff; }
.btn-responder:hover { background-color: #0056b3; }

.btn-pasapalabra { background-color: #ffc107; color: #333; }
.btn-pasapalabra:hover { background-color: #e0a800; }

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