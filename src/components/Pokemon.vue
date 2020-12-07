<template>
    <div class="container">
        <h1 class="text-center">Pokedex</h1>
        <PokemonList :imageUrl="imageUrl" :apiUrl="apiUrl" @setPokemonUrl="setPokemonUrl"/>
    </div>
</template>
<script>
import PokemonList from './List';

export default {
    data() {
        return {
            imageUrl: 'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/',
            apiUrl: 'https://pokeapi.co/api/v2/pokemon/'
        }
    },
    components: {
        PokemonList
    }
}
</script>
<style lang="scss">
body{
  background: rgb(255,255,255);
  background: linear-gradient(0deg, rgba(255,255,255,1) 0%, rgba(226,226,226,1) 51%, rgba(235,235,235,1) 100%);
}
</style>