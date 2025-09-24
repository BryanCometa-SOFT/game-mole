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

  // const hammerX = ref(0)
  // const hammerY = ref(0)

  // const hammerImg = ref(hammerUpImg)

  //Intervalos
  let intervalId = null

  // función para generar un número random entre 1 y totalItems
  function getRandomMole() {
    return Math.floor(Math.random() * totalItems.value) + 1
  }


  // function moveHammer(event) {
  //   hammerX.value = event.clientX - 150;
  //   hammerY.value = event.clientY
  // }

  // function hammerDown() {
  //   hammerImg.value = hammerDownImg
  // }

  // function hammerUp() {
  //   hammerImg.value = hammerUpImg
  // }

  function failAtack(){
    //Incremento el total de muertes
    totalFailMole.value++;
    
    //Reposiciono al topo
    currentMole.value = getRandomMole();
  }

  //Muerte de topo.
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

  onMounted(() => {
    // cada 500ms cambiamos el topo
    intervalId = setInterval(() => {
      currentMole.value = getRandomMole()
      console.log('Topo actual:', currentMole.value)
    }, 1000)
  })
</script>

<template>
  <div class="img-base-background no-select custom-cursor">
    <div class="overlay">

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
        <audio autoplay loop>
          <source src="@/assets/sounds/music-mole.mp3" type="audio/mpeg" />
          Tu navegador no soporta audio
        </audio>

        <div class="container-fluid">
            <div class="row mt-2">
              <div class="col-12 text-center">
                <div class="card shadow-lg border-0 text-center p-1 m-auto px-3" style=" border-radius: 1rem;">
                  <div class="card-body">
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

</style>