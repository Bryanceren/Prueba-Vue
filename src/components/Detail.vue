<template>
  <div class="container">
    <div class="row mb-4">
      <img :src="imageUrl" width="200" height="200" alt="" class="rounded-circle mx-auto bg-custom" >
    </div>
    <table class="table">
      <thead>
        <tr>
          <th scope="col">Atributo</th>
          <th scope="col">Valor</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Base Experience</td>
          <td>{{ pokemon.base_experience }} XP</td>
        </tr>
        <tr>
          <td>Height</td>
          <td>{{ pokemon.height / 10 }} m</td>
        </tr>
        <tr>
          <td>Weight</td>
          <td>{{ pokemon.weight / 10 }} kg</td>
        </tr>
      </tbody>
    </table>
    <hr>
    <h3>Tipos</h3>
    <div class="row my-3">
        <span class="badge badge-pill badge-primary ml-3" 
          v-for="(value, index) in pokemon.types"
          :key="'value'+index"><h6>{{ value.type.name }}</h6>
        </span>  
    </div>
    <hr>
    <h3>Habilidades</h3>
    <div class="row my-3">
        <div class="ability" 
          v-for="(value, index) in pokemon.abilities"
          :key="'value'+index">
          {{ value.ability.name }}
        </div>
    </div>
    <button type="button" class="btn btn-danger">Danger</button>
  </div>
</template>

<script>
  export default {
    props: [
      'pokemonUrl',
      'imageUrl'
    ],
    data: () => {
      return {
        show: false,
        pokemon: {}
      }      
    },
    methods: {
      fetchData() {
        let req = new Request(this.pokemonUrl);
        fetch(req)
          .then((resp) => {
            if(resp.status === 200)
              return resp.json();
          })
          .then((data) => {
            this.pokemon = data;
            this.show = true;
          })
          .catch((error) => {
            console.log(error);
          })
      },
      closeDetail() {
        this.$emit('closeDetail');
      }
    },
    created() {
      this.fetchData();
    }
  }
</script>
<style lang="scss" scoped>
.bg-custom{
  background: rgb(255,255,255);
  background: linear-gradient(0deg, rgb(204, 204, 204) 0%, rgba(226,226,226,1) 51%, rgba(235,235,235,1) 100%);
}
</style>