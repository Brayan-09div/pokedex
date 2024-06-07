<template>
  <q-layout view="hHh lpR fFf">
    <q-header elevated class="bg-primary text-white custom-header">
      <q-toolbar>
        <q-toolbar-title>
          <q-avatar>
            <img
              src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c2/Design_plat_Pok%C3%A9ball.svg/2048px-Design_plat_Pok%C3%A9ball.svg.png"
              alt="Logo">
          </q-avatar>
          <span class="pokemon-title">PokeDex</span>
        </q-toolbar-title>
      </q-toolbar>

      <q-tabs align="left">
        <q-route-tab to="/page1" label="Buscar Pokemon" />
        <q-route-tab to="/page2" label="Adivinar Pokemon" />
      </q-tabs>
    </q-header>

    <q-page-container>
      <q-page class="q-pa-md app-background">
        <div>
          <div class="buscador">
            <div class="input-container">
              <q-input outlined v-model="pokemonId" class="flex-item" :rules="[v => !!v || 'ID requerido']" />
              <q-btn color="primary" label="Buscar Poke" @click="triggerSearch" class="flex-item" />
            </div>
          </div>

          <div v-if="showAnimation" class="overlay">
            <PokeballAnimation />
          </div>

          <q-spinner v-if="loading" size="50px" color="primary" />

          <q-banner v-if="errorMessage" class="bg-red-5 text-white">
            {{ errorMessage }}
          </q-banner>

          <div v-show="ver == true">
            <!-- Contenido del Pokémon -->
            <div class="container">
              <div class="content">
                <div class="image-container">
                  <img :src="imgPokemon" :alt="`Imagen de ${Nombre}`" />
                </div>
                <div class="info-container">
                  <div class="pokemon-info">
                    <span class="info-label">Nombre:</span>
                    <span class="info-value">{{ Nombre }}</span>
                  </div>
                  <div class="pokemon-info">
                    <span class="info-label">Tipo:</span>
                    <div class="type-buttons">
                      <button v-for="(type, index) in tipo" :key="index" class="type-button"
                        :class="{ 'active': selectedType === type }" :style="{ backgroundColor: tipoColorMap[type] }"
                        @click="setType(type)" @keydown.enter="setType(type)">
                        {{ type }}
                      </button>
                    </div>
                  </div>
                  <div class="pokemon-info">
                    <span class="info-label">Altura:</span>
                    <span class="info-value">{{ AlturaCm }} cm ({{ AlturaM }} m)</span>
                  </div>
                  <div class="pokemon-info">
                    <span class="info-label">Peso:</span>
                    <span class="info-value">{{ PesoKg }} kg</span>
                  </div>
                  <div class="pokemon-info">
                    <span class="info-label">Descripción:</span>
                    <span class="info-value">{{ descripcion }}</span>
                  </div>
                  <div class="pokemon-info">
                    <span class="info-label">ID:</span>
                    <span class="info-value">{{ id }}</span>
                  </div>
                </div>
              </div>
            </div>

            <div style="display: flex; justify-content: center; align-items: center;">
              <div class="stats-container">
                <div class="stat-bar-container">
                  <div class="stat-label">HP</div>
                  <q-linear-progress rounded size="20px" :value="stackHp / 100" color="red" class="q-mt-sm" />
                  <div class="stat-value">{{ stackHp }}</div>
                </div>
                <div class="stat-bar-container">
                  <div class="stat-label">Ataque</div>
                  <q-linear-progress rounded size="20px" :value="stackataque / 100" color="warning" class="q-mt-sm" />
                  <div class="stat-value">{{ stackataque }}</div>
                </div>
                <div class="stat-bar-container">
                  <div class="stat-label">Defensa</div>
                  <q-linear-progress rounded size="20px" :value="stackdefensa / 100" color="silver" class="q-mt-sm" />
                  <div class="stat-value">{{ stackdefensa }}</div>
                </div>
                <div class="stat-bar-container">
                  <div class="stat-label">Ataque Especial</div>
                  <q-linear-progress rounded size="20px" :value="stackSpecialAttack / 100" color="purple"
                    class="q-mt-sm" />
                  <div class="stat-value">{{ stackSpecialAttack }}</div>
                </div>
                <div class="stat-bar-container">
                  <div class="stat-label">Defensa Especial</div>
                  <q-linear-progress rounded size="20px" :value="stackspecialDefense / 100" color="green"
                    class="q-mt-sm" />
                  <div class="stat-value">{{ stackspecialDefense }}</div>
                </div>
                <div class="stat-bar-container">
                  <div class="stat-label">Velocidad</div>
                  <q-linear-progress rounded size="20px" :value="stackSpeed / 100" color="pink" class="q-mt-sm" />
                  <div class="stat-value">{{ stackSpeed }}</div>
                </div>
              </div>
            </div>

            <div class="gallery-button-container">
              <q-btn color="secondary" push icon="photo_library" label="Abrir Galería" @click="openGallery" />
            </div>

            <q-dialog v-model="isGalleryOpen">
              <q-card>
                <q-card-section>
                  <div class="text-h6">Galería de Imágenes</div>
                </q-card-section>
                <q-card-section>
                  <div class="image-gallery">
                    <div v-if="imgPokemon2" class="thumbnail" @click="enlargeImage(imgPokemon2)">
                      <img :src="imgPokemon2" alt="Imagen alternativa de {{ Nombre }}" />
                    </div>
                    <div v-if="imgPokemon3" class="thumbnail" @click="enlargeImage(imgPokemon3)">
                      <img :src="imgPokemon3" alt="Imagen trasera de {{ Nombre }}" />
                    </div>
                    <div v-if="imgPokemon4" class="thumbnail" @click="enlargeImage(imgPokemon4)">
                      <img :src="imgPokemon4" alt="Imagen delantera de {{ Nombre }}" />
                    </div>
                    <div v-if="imgPokemon5" class="thumbnail" @click="enlargeImage(imgPokemon5)">
                      <img :src="imgPokemon5" alt="Imagen de showdown de {{ Nombre }}" />
                    </div>
                  </div>
                </q-card-section>
                <q-card-actions align="center">
                  <q-btn flat label="Cerrar" color="primary" v-close-popup />
                </q-card-actions>
              </q-card>
            </q-dialog>

            <q-dialog v-model="isImageEnlarged" persistent>
              <q-card>
                <q-card-section>
                  <img :src="selectedImage" class="enlarged-image" />
                </q-card-section>
                <q-card-actions align="center">
                  <q-btn flat label="Cerrar" color="primary" v-close-popup />
                </q-card-actions>
              </q-card>
            </q-dialog>
          </div>
        </div>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import PokeballAnimation from './components/pokeball.vue';
import { Dialog } from "quasar";

const isGalleryOpen = ref(false);
const isImageEnlarged = ref(false);
const selectedImage = ref('');

function openGallery() {
  isGalleryOpen.value = true;
}

function enlargeImage(image) {
  selectedImage.value = image;
  isImageEnlarged.value = true;
}

const tipoColorMap = {
  electric: "#FFD700",
  water: "#1E90FF",
  fire: "#FF4500",
  ground: "#8B4513",
  grass: "#32CD32",
  steel: "#808080",
  bug: "#708090",
  dragon: "#483D8B",
  fairy: "#FF69B4",
  ice: "#ADD8E6",
  fighting: "#800000",
  normal: "#A9A9A9",
  poison: "#9932CC",
  psychic: "#800080",
  rock: "#696969",
  dark: "#2F4F4F",
  flying: "#4682B4",
  ghost: "#705898"
};

// Imágenes
let imgPokemon = ref("");
let imgPokemon2 = ref("");
let imgPokemon3 = ref("");
let imgPokemon4 = ref("");
let imgPokemon5 = ref("");

let ver = ref(false);
let id = ref("");

let pokemonId = ref("");
let Nombre = ref("");
let tipo = ref([]);
let AlturaCm = ref("");
let AlturaM = ref("");
let PesoKg = ref("");
let Hp = ref("");
let stackHp = ref("");
let ataque = ref("");
let stackataque = ref("");
let defensa = ref("");
let stackdefensa = ref("");
let specialAttack = ref("");
let stackSpecialAttack = ref("");
let specialDefense = ref("");
let stackspecialDefense = ref("");
let speed = ref("");
let stackSpeed = ref("");
let showAnimation = ref(false);
let selectedType = ref("");

function setType(type) {
  selectedType.value = type;
}

async function triggerSearch() {
  showAnimation.value = true;
  setTimeout(() => {
    showAnimation.value = false;
    traer();
  }, 3000);
}

async function traer() {
  try {
    let res = await axios.get(
      `https://pokeapi.co/api/v2/pokemon/${pokemonId.value}`
    );

    // Imágenes
    imgPokemon.value = res.data.sprites.other["official-artwork"].front_default;
    imgPokemon2.value = res.data.sprites.other.dream_world.front_default;
    imgPokemon3.value = res.data.sprites.back_default;
    imgPokemon4.value = res.data.sprites.front_default;
    imgPokemon5.value = res.data.sprites.other.showdown.front_default;

    Nombre.value = res.data.forms[0].name;

    // Obtén los tipos del Pokémon
    tipo.value = res.data.types.map((typeInfo) => typeInfo.type.name);

    // Conversión de altura y peso
    AlturaCm.value = res.data.height * 10; // decímetros a centímetros
    AlturaM.value = (res.data.height / 10).toFixed(2); // decímetros a metros
    PesoKg.value = (res.data.weight / 10).toFixed(2); // hectogramos a kilogramos

    id.value = res.data.id;

    Hp.value = res.data.stats[0].stat.name;
    stackHp.value = res.data.stats[0].base_stat;

    ataque.value = res.data.stats[1].stat.name;
    stackataque.value = res.data.stats[1].base_stat;

    defensa.value = res.data.stats[2].stat.name;
    stackdefensa.value = res.data.stats[2].base_stat;

    specialAttack.value = res.data.stats[3].stat.name;
    stackSpecialAttack.value = res.data.stats[3].base_stat;

    specialDefense.value = res.data.stats[4].stat.name;
    stackspecialDefense.value = res.data.stats[4].base_stat;

    speed.value = res.data.stats[5].stat.name;
    stackSpeed.value = res.data.stats[5].base_stat;

    let bodyBackgroundColor = "#ffffff";

    tipo.value.forEach((tipoPokemon) => {
      if (tipoColorMap[tipoPokemon] && bodyBackgroundColor === "#ffffff") {
        bodyBackgroundColor = tipoColorMap[tipoPokemon];
      }
    });
    document.body.style.backgroundColor = bodyBackgroundColor;

    ver.value = true;

  } catch (error) {
    console.error("Error al obtener datos del Pokémon:", error);
  }
}
</script>


<style scoped>
.app-background {
  padding: 20px;
  border-radius: 8px;
}

@font-face {
  font-family: 'Pokemon';
  src: url('src/assets/fonts/PokemonSolid.ttf') format('truetype');
}

.pokemon-title {
  font-family: 'Pokemon', sans-serif;
  font-size: 40px;
  color: yellow;
  text-shadow: 2px 2px 0px red, 4px 4px 0px blue;
}

.custom-header {
  height: 60px;
}

.custom-header .q-toolbar-title {
  display: flex;
  align-items: center;
  gap: 10px;
}

.custom-header .q-avatar img {
  width: 30px;
  height: 30px;
}

.q-dialog .q-card {
  text-align: center;
  align-items: center;
}

.image-gallery-container {
  margin: 0 auto;
  padding: 20px;
}

.image-gallery {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
}

.gallery-button-container {
  position: fixed;
  top: 50%;
  right: 0;
  transform: translateY(-50%);
  display: flex;
  justify-content: flex-end;
  padding-right: 20px;
}

.thumbnail {
  cursor: pointer;
  width: 150px;
  height: 150px;
  overflow: hidden;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.thumbnail img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.2s ease-in-out;
}

.thumbnail:hover img {
  transform: scale(1.05);
}

.enlarged-image {
  width: 100%;
  max-width: 200px;
}

.type-button {
  padding: 5px 10px;
  margin-right: 5px;
  border: none;
  border-radius: 5px;
  color: white;
  cursor: pointer;
}

.type-button:hover {
  opacity: 0.8;
}

.type-button.active {
  border: 2px solid #000;
}

.buscador {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 5px;
}

.input-container {
  display: flex;
  gap: 10px;
  background-color: rgba(255, 255, 255, 0.419);
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  background-color: transparent;
  padding: 20px;
}

.content {
  display: flex;
  align-items: flex-start;
  gap: 20px;
  background-color: transparent;
}

.image-container img {
  width: 200px;
  border-radius: 8px;
}

.info-container {
  display: flex;
  flex-direction: column;
  gap: 10px;
  border-radius: 8px;
  padding: 10px;
  box-shadow: 0 2px 4px rgba(2, 2, 2, 0.841);
  background-color: rgba(255, 255, 255, 0.5);
}

.info-label {
  font-weight: bold;
  margin-right: 10px;
}

.info-value {
  font-size: 20px;
  color: #000000;
}

.stats-container {
  width: 80%;
  max-width: 600px;
  padding: 5px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(2, 2, 2, 0.841);
  background-color: rgba(255, 255, 255, 0.5);
}

.stat-bar-container {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  padding: 10px;
  border-radius: 8px;
}

.q-linear-progress__track {
  background-color: transparent;
}

.q-linear-progress__inner {
  height: 15px;
}

.stat-label {
  margin-right: 10px;
  font-weight: bold;
  width: 60px;
}

.stat-value {
  margin-left: 10px;
  font-weight: bold;
}

.overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgb(255, 205, 5);
  z-index: 9999;
}

@media (max-width: 768px) {
  .pokemon-title {
    font-size: 1.5rem;
  }

  .custom-header .q-avatar img {
    width: 20px;
    height: 20px;
  }

  .input-container {
    flex-direction: column;
    gap: 0.5rem;
  }

  .content {
    flex-direction: column;
    align-items: center;
  }

  .gallery-button-container {
    position: static; /* Cambia la posición para que sea estática en pantallas pequeñas */
    display: flex;
    justify-content: center;
    width: 100%;
    padding-right: 0;
    margin-top: 10px;
  }

  .thumbnail {
    width: 100px;
    height: 100px;
  }

  .info-container {
    max-width: 100%;
    padding: 0.5rem;
  }

  .stats-container {
    max-width: 100%;
    padding: 0.5rem;
  }

  .stat-bar-container {
    padding: 0.5rem;
  }

  .gallery-button {
    padding: 8px 16px;
  }
}

@media (max-width: 480px) {
  .pokemon-title {
    font-size: 1rem;
  }

  .input-container {
    padding: 0.5rem;
    gap: 0.25rem;
  }

  .thumbnail {
    width: 75px;
    height: 75px;
  }

  .info-container {
    padding: 0.25rem;
  }

  .stats-container {
    padding: 0.25rem;
  }

  .stat-bar-container {
    padding: 0.25rem;
  }

  .gallery-button {
    padding: 6px 12px;
  }
}
</style>
