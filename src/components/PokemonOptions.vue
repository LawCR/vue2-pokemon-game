<template>
  <div class="options-container">
    <ul>
      <li
        v-for="pokemon in pokemons"
        :key="pokemon.id"
        
      >
        <button
          @click="$emit('selectPokemon', pokemon.id)"
          :class="optionClass(pokemon.id)"
          :disabled="optionSelected"
        >
          {{ pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1) }}
        </button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  props: {
    pokemons: {
      type: Array,
      required: true,
    },
    optionSelected: {
      type: [Number, null],
      required: false,
    },
    pokemon: {
      type: [Object, null],
      required: false,
    },
  },
  methods: {
    optionClass(pokemonId) {
      if (!this.optionSelected) return '';
      if (this.optionSelected === pokemonId && pokemonId !== this.pokemon?.id) return 'option-incorrect';
      if (pokemonId === this.pokemon?.id) return 'option-correct';
      return '';
    },
  }
}
</script>

<style scoped>
/* Pokemon Options */
ul {
  list-style-type: none;
  padding: 0;
}

button {
  background-color: white;
  border-radius: 5px;
  border: 1px solid rgba(0, 0, 0, 0.2);
  cursor: pointer;
  margin-bottom: 10px;
  width: 250px;
  color: #000;
  text-align: center;
  font-weight: 600;
  padding: 8px;
}

button:disabled {
  cursor: default;
}

button:hover {
  opacity: 0.9;
}
.option-correct {
  background-color: #4caf50;
  color: white;
}

.option-incorrect {
  background-color: #f44336;
  color: white;
}


.options-container {
  display: flex;
  justify-content: center;
  padding-top: 20px;
}
</style>