<template>

  <div class="card mb-3 mx-auto" style="width: 18rem;">
    <div class="card-body">
      <h5>{{pokemon.name}}</h5>
      <hr>
      <div class="row mb-4">
        <img :src="imageUrl" width="200" height="200" alt="" class="rounded-circle mx-auto bg-custom" draggable="false" >
      </div>
      
      <div class="row my-3 ml-1">
          <b-badge pill variant="primary" class="ml-2" :class="value.type.name"
            v-for="(value, index) in pokemon.types"
            :key="'value'+index"><h6>{{ value.type.name }}</h6></b-badge>  
      </div>
      <b-button @click="modalShow = !modalShow" block variant="success" :id="teamName+'pokemon.id'">Ver Mas</b-button>
      <b-modal v-model="modalShow" :title="pokemon.name" hide-footer>
        <Detail :pokemonUrl="pokemonUrl"
          :imageUrl="imageUrl"/>
          <hr>
          <div class="row mx-auto" v-if="teamName=='none'">
            <b-button class="col-5 mx-auto" variant="primary" @click="addPokemon(pokemon.id, pokemon.name,pokemonUrl,1)">Agregar a equipo 1</b-button>
            <b-button class="col-5 mx-auto" variant="primary" @click="addPokemon(pokemon.id, pokemon.name,pokemonUrl,2)">Agregar a equipo 2</b-button>
          </div>
        <b-button class="mt-3" block variant="danger" @click="modalShow = !modalShow">Cerrar</b-button>
      </b-modal>
    </div>
  </div>
</template>

<script>
import Detail from './Detail';

  export default {
    props: [
      'pokemonUrl',
      'imageUrl',
      'teamName'
    ],
    data: () => {
      return {
        show: false,
        pokemon: {},
        modalShow: false
      }      
    },
    components: {
      Detail
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
      },
      addPokemon(id,name,url,team){
        var poke = new Object();
        poke.id = id;
        poke.name  = name;
        poke.url = url;
        poke.team = team;
        var jsonString= JSON.stringify(poke);
        var pokemon = JSON.parse(jsonString);
        this.$emit('agregar',pokemon);
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