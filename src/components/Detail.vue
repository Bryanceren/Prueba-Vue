<template>
  <div class="container">
    <div class="row mb-4">
      <img :src="imageUrl" width="200" height="200" alt="" class="rounded-circle mx-auto"  >
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
    <div class="row my-3 ml-1">
          <b-badge pill variant="primary" class="ml-2" :class="value.type.name"
            v-for="(value, index) in pokemon.types"
            :key="'value'+index"><h6>{{ value.type.name }}</h6></b-badge>  
      </div>
    <hr>
    <h3>Habilidades</h3>
    <div class="row my-3 ml-1">
        <b-badge pill variant="success" class="ml-2" 
          v-for="(value, index) in pokemon.abilities"
          :key="'value'+index"><h6>{{ value.ability.name }}</h6></b-badge>
    </div>
    
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

.normal {
	background-color: #A8A878;
}

.fire {
	background-color: #F08030;
}

.water {
	background-color: #6890F0;
}

.electric {
	background-color: #F8D030;
}

.grass {
	background-color: #78C850;
}

.ice {
	background-color: #98D8D8;
}

.ground {
	background-color: #E0C068;
}

.flying {
	background-color: #A890F0;
}

.ghost {
	background-color: #705898;
}

.rock {
	background-color: #B8A038;
}

.fighting {
	background-color: #C03028;
}

.poison {
	background-color: #A040A0;
}

.psychic {
	background-color: #F85888;
}
.fairy {
	background-color: #d6b6c0;
}
.bug {
	background-color: #A8B820;
}

.dark {
	background-color: #705848;
}

.steel {
	background-color: #B8B8D0;
}

.dragon {
	background-color: #7038F8;
}
</style>