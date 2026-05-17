<template>
  <div class="game-container">
    <h1>El Rosco - Pasapalabra</h1>
    <div class="rosco-container">
      <div class="center-content">
        <p>¡Preparado!</p>
      </div>
      
      <div
        v-for="(letter, index) in letters"
        :key="index"
        class="letter-circle"
        :style="getLetterStyle(index)"
      >
        {{ letter.char }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const alphabet = 'ABCDEFGHIJKLMNÑOPQRSTUVWXYZ'.split('');

const letters = ref(
  alphabet.map(char => ({
    char,
    status: 'pending'
  }))
);

const getLetterStyle = (index) => {
  const totalLetters = letters.value.length;
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

h1 {
  color: #333;
  margin-bottom: 20px;
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
  font-size: 1.5rem;
  color: #555;
}

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
}
</style>
