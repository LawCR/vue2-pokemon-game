<template>
  <h1 v-if="!pokemon">Cargando...</h1>
  <div v-else class="pokemon-page">
    <h1>¿Quién es este pokémon?</h1>
    <div class="score-container">
      <h3>Puntación Actual: {{ score }}</h3>
      <h3>Puntaje máximo: {{ maxScore }}</h3>
    </div>
    <PokemonPicture :pokemon-id="pokemon.id" :show-pokemon="showPokemon" />
    <h2 v-if="showAnswer" :class="['fade-in', answerClass].join(' ')">{{ message }}</h2>
    <pokemonOptions
      :pokemons="pokemonArr"
      :pokemon="pokemon"
      :optionSelected="optionSelected"
      @select-pokemon="checkAnswer" 
    />

    <button @click="newGame" v-if="showAnswer" class="fade-in">
      {{ optionSelected !== pokemon.id ? 'Intentar de nuevo' : 'Siguiente' }}
    </button>
  </div>
</template>

<script>
import PokemonOptions from '@/components/PokemonOptions.vue';
import PokemonPicture from '@/components/PokemonPicture.vue';
import getPokemonOptions from '@/helpers/getPokemonOptions';

export default {
  components: {
    PokemonOptions,
    PokemonPicture,
  },
  data() {
    return {
      pokemonArr: [],
      pokemon: null,
      showPokemon: false,
      showAnswer: false,
      message: '',
      optionSelected: null,
      score: 0,
      maxScore: 0,
    };
  },
  methods: {
    async mixPokemonOptions() {
      this.pokemonArr = await getPokemonOptions();

      const randomIndex = Math.floor(Math.random() * this.pokemonArr.length);
      this.pokemon = this.pokemonArr[randomIndex];
    },
    checkAnswer(pokemonIdSelected) {
      this.showPokemon = true
      this.optionSelected = pokemonIdSelected;

      if (pokemonIdSelected === this.pokemon.id) {
        this.message = `¡Correcto! ${this.pokemon.name} es el pokémon correcto.`
        this.score += 1;
        if (this.score > this.maxScore) {
          this.maxScore = this.score;
          localStorage.setItem('maxScore', this.maxScore);
        }
      } else {
        this.message = `Perdiste, el pokémon  era "${this.pokemon.name}".`
        // this.score = 0;
      }

      this.showAnswer = true;
    },
    newGame() {
      if (this.optionSelected !== this.pokemon?.id) this.score = 0;
      this.pokemon = null;
      this.optionSelected = null;
      this.showPokemon = false;
      this.showAnswer = false;
      this.message = '';
      this.pokemonArr = [];
      this.mixPokemonOptions();
    },
  },
  mounted() {
    this.mixPokemonOptions();
    this.maxScore = localStorage.getItem('maxScore') || 0;
  },
  computed: {
    answerClass() {
      if (!this.pokemon) return '';
      if (this.optionSelected === this.pokemon.id) return 'success';
      return 'error';
    },
  }
};

</script>

<style scoped>
.pokemon-page {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: white;
}

.score-container {
  display: flex;
  justify-content: space-between;
  width: 100%;
  margin-top: 8px;
  margin-bottom: 20px;
}

h2 {
  margin-top: 12px;
  color: white;
  font-size: 18px;
  padding: 4px 16px 8px 16px;
  border-radius: 8px;
  background-color: #f44336;
}

.success {
  background-color: #4caf50;
}

.error {
  background-color: #f44336;
}

button {
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 8px;
  padding: 12px 24px;
  font-size: 16px;
  cursor: pointer;
  margin-top: 20px;
}
button:hover {
  background-color: #388e3c;
}
</style>