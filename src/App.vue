<template>
  <div id="app">
    <Pokemon />
  </div>
</template>
<script>

import Pokemon from './components/Pokemon.vue'

export default {
  components: {
    Pokemon
  }
}
</script>

<style lang="scss">
  @import url('https://use.fontawesome.com/releases/v5.8.2/css/all.css');
  @import url('https://fonts.googleapis.com/css2?family=Roboto&display=swap');
*{
font-family: 'Roboto', sans-serif;

}
</style>