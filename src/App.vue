<script setup>
  import { ref, onMounted, watch } from "vue";
  import hammerUpImg from '@/assets/img/hammer.png'
  import hammerDownImg from '@/assets/img/hammer-down.png'

  // Variables reactivas del componente
  const totalItems = ref(12);

  // número actual del topo (reactivo)
  const currentMole = ref(null);

  const totalDeathMole = ref(0);
  const totalFailMole = ref(0);

  //Configuraciones de audio
  const audio = ref(null) 
  const isPlaying = ref(false)

  //Intervalos
  let intervalId = null;

  //Niveles de dificultad
  const levels = [
    { id: 1, name: '⚡️ Dios del Olimpo', value: 150 },
    { id: 2, name: '🛡️ Guerrero Espartano', value: 300 },
    { id: 3, name: '🪰 Mata Moscas', value: 600 },
    { id: 4, name: '🦆 Cazador de Patos', value: 1000 },
    { id: 5, name: '🦋 Mata Mariposas', value: 1500 },
    { id: 6, name: '👑 Eres una Princesa', value: 2000 },
  ]

  // Nivel actual
  const selectedLevelId = ref(3) // por defecto Mata Moscas

  // función para generar un número random entre 1 y totalItems
  function getRandomMole() {
    return Math.floor(Math.random() * totalItems.value) + 1
  }

  //Cada vez que se ataca fallidamente al topo
  function failAtack(){
    //Incremento el total de muertes
    totalFailMole.value++;
    
    //Reposiciono al topo
    currentMole.value = getRandomMole();
  }

  //Cada vez que se ataca al topo correctamente
  function deathMole() {
    //Incremento el total de muertes
    totalDeathMole.value++;

    //Reposiciono al topo
    currentMole.value = getRandomMole();
  }

  //Reinicio del juego
  function resetGame() {
    totalDeathMole.value = 0;
    totalFailMole.value = 0;
  }

  //Ejecutar musica
  function playMusic() {
    if (audio.value && !isPlaying.value) {
      audio.value.play().then(() => {
        isPlaying.value = true
      }).catch(err => {
        console.warn('No se pudo reproducir el audio:', err)
      })
    }
  }

  //Permite el cambio del nivel
  function changeLevel() {
    //Almaceno el nuevo nivel en el local storage
    localStorage.setItem('moleLevelId', selectedLevelId.value)

   //Inicio el juego
    startMoleInterval();
  }

  //Inicio del tiempo del movimiento del topo
  function startMoleInterval() {
    //Si existe el invervalo de tiempo lo destruyo
    if (intervalId) {
      clearInterval(intervalId);
    } 

    //Obtengo los datos del nivel configurado
    const level = levels.find(l => l.id === selectedLevelId.value)

    //Ejecuto un nuevo tiempo.
    intervalId = setInterval(() => {
      currentMole.value = getRandomMole()
    }, level.value) // Diferentes nivels
  }

  onMounted(() => {
    //Si existe el nivel configurado en el sistema del navegador
    const savedId = localStorage.getItem('moleLevelId');

    //Busco mi nivel guardado y obtengo el id para almacenarlo en el nivel actual
    if (savedId && levels.some(l => l.id === savedId)) {
      selectedLevelId.value = savedId
    } else {
      selectedLevelId.value = 3
    }

    //Inicio el juego
    startMoleInterval();
  })

</script>

<template>
  <div class="img-base-background no-select custom-cursor">
    <div class="overlay" @click="playMusic" @touchstart="playMusic">

      <!-- <img
        @mousedown="hammerDown"
        @mouseup="hammerUp"
        @click="failAtack()"
        :src="hammerImg"
        width="250px"
        style="z-index: 2;"
        :style="{ position: 'fixed', top: hammerY + 'px', left: hammerX + 'px', transform: 'translate(-50%, -50%)' }"
        class="animate-pulse"
      /> -->
          <!-- <div @mousemove="moveHammer"></div> -->

      <div>
        <audio ref="audio" loop>
          <source src="@/assets/sounds/music-mole.mp3" type="audio/mpeg">
          Tu navegador no soporta audio
        </audio>
        

        <div class="container-fluid">
            <div class="row mt-2">
              <div class="col-12 text-center">
                <div class="card shadow-lg border-0 text-center m-auto px-3" style=" border-radius: 1rem;">
                  <div class="card-body py-1 pb-2">
                    <!-- Título principal responsive -->
                      <h1 class="d-none d-lg-block font-weight-bold text-dark">
                        🐹 Mata al Topo 
                      </h1>

                      <h5 class="d-block d-lg-none font-weight-bold text-dark">
                        🐹 Mata al Topo
                      </h5>

                      <!-- Subtítulo responsive -->
                      <h2 class="mt-3 text-muted h5 h-sm4 h-md3">
                        Intentos: 
                        <span class="badge badge-danger badge-pill pointer">
                          {{ totalFailMole }}  
                        </span>
                        -
                        Asiertos: 
                        <span class="badge badge-success badge-pill pointer">
                          {{ totalDeathMole }}  
                        </span>

                        <span title="Reiniciar" class="pointer ml-2" @click="resetGame()">
                          🔄
                        </span>                       
                      </h2>

                      <select class="form-control level-select m-auto mt-2" 
                        v-model="selectedLevelId" 
                        @change="changeLevel()">
                        <option v-for="level in levels" :key="level.id" :value="level.id">
                          {{ level.name }} ({{ (level.value / 1000).toFixed(1) }}) s
                        </option>
                      </select>
                  </div>
                </div>
              </div>
          
            <!-- Topos y animación-->
            <div 
              v-for="item in totalItems" 
              :key="item" 
              class="col-6 col-sm-4 col-md-4 col-xl-3 d-flex justify-content-center align-items-center mt-4">
              
              <img
                v-if="currentMole === item"
                src="@/assets/img/mole.png"
                class="w-75 animate-pop"
                @click="deathMole()"
                draggable="false"
              />
              <img
                v-else
                @click="failAtack()"
                src="@/assets/img/not-mole.png"
                class="w-75 animate-pulse"
                draggable="false"
              />
            </div>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<style scoped>
:global(html, body, #app) {
  height: 100%;
  margin: 0;
}

.custom-cursor {
  cursor: url('@/assets/img/hammer.png'), auto;
}

.clicked-cursor {
  cursor: url('@/assets/img/hammer-down.png'), auto;
}

.img-base-background {
  position: fixed;    
  top: 0;
  left: 0;
  width: 100vw;         
  height: 100vh;       
  background-image: url('@/assets/img/background.jpg');
  background-size: cover; 
  background-position: center;
  background-repeat: no-repeat;

}

.overlay {
  position: relative;
     
}

/* Animación "pop" */
.pop-enter-active {
  animation: pop-in 0.3s ease-out forwards;
}
.pop-leave-active {
  animation: pop-out 0.2s ease-in forwards;
}

/* Animación constante en todos los huecos */
@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(0.95); }
  100% { transform: scale(1); }
}

.animate-pulse {
  animation: pulse 1s infinite;
}

/* Animación para el topo cuando aparece */
@keyframes pop {
  0% { transform: scale(0.2); opacity: 0; }
  80% { transform: scale(1.1); opacity: 1; }
  100% { transform: scale(1); }
}

.animate-pop {
  animation: pop 0.3s ease-out;
}

/* Bloquea selección de texto */
.no-select {
  user-select: none;      /* Standard */
  -webkit-user-select: none; /* Safari */
  -moz-user-select: none; /* Firefox */
  -ms-user-select: none;  /* IE/Edge */
}

.level-select {
  width: 100%; /* ancho por defecto móvil */
}

@media (min-width: 768px) { /* tablet */
  .level-select {
    width: 50%;
  }
}

@media (min-width: 992px) { /* desktop */
  .level-select {
    width: 25%;
  }
}

</style>