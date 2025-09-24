<script setup>
  import { ref, onMounted, watch } from "vue";

  // Variables reactivas del componente
  const totalItems = ref(12);

  // número actual del topo (reactivo)
  const currentMole = ref(null);

  let totalDeathMole = 0;

  //Intervalos
  let intervalId = null

  // función para generar un número random entre 1 y totalItems
  function getRandomMole() {
    return Math.floor(Math.random() * totalItems.value) + 1
  }

  //Muerte de topo.
  function deathMole() {
    //Incremento el total de muertes
    totalDeathMole ++;

    //Reposiciono al topo
    currentMole.value = getRandomMole();
  }

  //Reinicioel juego
  function resetGame() {
    totalDeathMole = 0;
  }

  onMounted(() => {
    // cada 500ms cambiamos el topo
    intervalId = setInterval(() => {
      currentMole.value = getRandomMole()
      console.log('Topo actual:', currentMole.value)
    }, 10000)
  })
</script>

<template>
  <div class="img-base-background">
    <div class="overlay">

       <audio autoplay loop>
        <source src="@/assets/sounds/music-mole.mp3" type="audio/mpeg" />
        Tu navegador no soporta audio
      </audio>

      <div class="container-fluid">
          <div class="row mt-2">

            <div class="col-12 text-center">
              <div class="card shadow-lg border-0 text-center p-1 m-auto" style="max-width: 500px; border-radius: 1rem;">
                <div class="card-body">
                  <h1 class="display-4 font-weight-bold text-dark">
                    🐹 Mata al Topo
                  </h1>
                  <h2 class="mt-3 text-muted">
                    Total de muertes: 
                    <span class="badge badge-danger badge-pill">
                      {{ totalDeathMole }}  
                    </span>

                    <span title="Reiniciar" class="pointer" @click="resetGame()">
                      🔄
                    </span>
                  </h2>
                </div>
              </div>
            </div>
         

          <div v-for="item in totalItems" :key="item" class="col-3 d-flex justify-content-center align-items-center mt-4">
            <img
              v-if="currentMole === item"
              src="@/assets/img/mole.png"
              class="w-75 animate-pop"
              @click="deathMole()"
            />
            <img
              v-else
              src="@/assets/img/not-mole.png"
              class="w-75 animate-pulse"
            />

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
</style>