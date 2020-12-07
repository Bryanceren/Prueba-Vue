<template>
    <div class="container my-5" >
      <div class="row mb-3">
        <b-button @click="removeAll" variant="danger" class="mx-auto">Quitar todos</b-button>
      </div>

      <div v-if="repetido==true">
          {{ToastRepetidos()}}
          {{repetido=false}}
      </div>
      <div v-if="limite==true">
          {{ToastLimite()}}
          {{limite=false}}
      </div>
        <div class="row">
          <div class="col-lg-6 col-md-12 col-sm-12 col-xs-12">
            <div class="card rounded">
              <div class="card-body pb-0">
                <h5 class="card-title d-inline">Equipo 1</h5>
                <hr>
                  <i class="fas fa-plus-square d-inline mr-2"></i>
                  <p class="card-text d-inline">Arrastra tus pokemons aqui</p>
              </div>
              <!-- <div v-for='pokemon in Team1' :key='pokemon.name' class='drag-el'>
                {{ pokemon.name }}
              </div> -->
              <div class="card-body" id="board-1" @dragover.prevent @drop.prevent="drop($event,team1)" >
                <div 
                  v-for="(pokemon, index) in team1"
                  :key="'poke'+index" class="col col-lg-4 " 
                  :id="'idteam1' + index" 
                  @dragstart="dragStart($event,pokemon)"
                  @dragover.stop
                  draggable="true"
                  >
                  <Poke :pokemonUrl="pokemon.url" :imageUrl="imageUrl + pokemon.id + '.png'" :teamName="'team1'"/>                      
                </div>
              </div>

            </div>
          </div>
          <div class="col-lg-6 col-md-12 col-sm-12 col-xs-12">
            <div class="card">
              <div class="card-body pb-0">
                <h5 class="card-title d-inline">Equipo 2</h5>
                <hr>
                  <i class="fas fa-plus-square d-inline mr-2"></i>
                  <p class="card-text d-inline">Arrastra tus pokemons aqui</p>
              </div>
              <div class="card-body" id="board-2" @dragover.prevent @drop.prevent="drop($event,team2)">
                <div 
                  v-for="(pokemon, index) in team2"
                  :key="'poke'+index" class="col col-lg-4 " 
                  :id="'idteam2' + index" 
                  @dragstart="dragStart($event,pokemon)"
                  @dragover.stop
                  draggable="true">
                  <Poke :pokemonUrl="pokemon.url" :imageUrl="imageUrl + pokemon.id + '.png'" :teamName="'team2'" />                      
                </div>
              </div>
            </div>
          </div>
        </div>
        <hr>
        <div class="row justify-content-md-center center mx-auto" >
            <div 
              v-for="(pokemon, index) in pokemons.slice(0, 150)"
              :key="'poke'+index" class="col col-lg-4 " 
              :id="'id' + index" 
              @dragstart="dragStart($event,pokemon)"
              @dragover.stop
              draggable="true"
              >
              <Poke :pokemonUrl="pokemon.url" :imageUrl="imageUrl + pokemon.id + '.png'" :teamName="'none'" @agregar="agregarEmit"/>                      
            </div>
            <div id="scroll-trigger" ref="infinitescrolltrigger">
            <i class="fas fa-spinner fa-spin"></i>
            </div>
        </div>
    </div>
    
</template>
<script>
import Detail from './Detail';
import Poke from './PokeCard';

  export default {
    props: [
      'imageUrl',
      'apiUrl'
    ],
    data: () => {
      return {
        pokemons: [],
        team1 :[],
        team2 :[],
        nextUrl: '',
        currentUrl: '',
        repetido: false,
        limite: false,
      }
    },
    components:{
      Detail,
      Poke
    },
    methods: {
      saveAll() {
        const pokes = JSON.stringify(this.pokemons);
        const team1 = JSON.stringify(this.team1);
        const team2 = JSON.stringify(this.team2);
        localStorage.setItem('pokemons', pokes);
        localStorage.setItem('team1', team1);
        localStorage.setItem('team2', team2);
      },
      removeAll(){
        this.team1=[];
        this.team2=[];
        this.saveAll();
      },
      ToastRepetidos(append = false) {
        this.$bvToast.toast(`no puedes agregar mas de dos pokemons iguales`, {
          title: 'Error!',
          autoHideDelay: 5000,
          appendToast: append
        })
      },
      ToastLimite(append = false) {
        this.$bvToast.toast(`No puedes tener mas de seis pokemons en un equipo!`, {
          title: 'Error!',
          autoHideDelay: 5000,
          appendToast: append
        })
      },
      fetchData() {
        let req = new Request(this.currentUrl);
        fetch(req)
          .then((resp) => {
            if(resp.status === 200)
              return resp.json();
          })
          .then((data) => {
            this.nextUrl = data.next;
            data.results.forEach(pokemon => {
              pokemon.id = pokemon.url.split('/')
                .filter(function(part) { return !!part }).pop();
              this.pokemons.push(pokemon);
            });
          })
          .catch((error) => {
            console.log(error);
          })
      },
      scrollTrigger() {
        const observer = new IntersectionObserver((entries) => {
          entries.forEach(entry => {
            if(entry.intersectionRatio > 0 && this.nextUrl) {
              this.next();
            }
          });
        });
        observer.observe(this.$refs.infinitescrolltrigger);
      },
      next() {
        this.currentUrl = this.nextUrl;
        this.fetchData();
      },
      dragStart: function(e,item) {
        const target = e.target;
        console.log(target.id);
        e.dataTransfer.setData('pokemon',JSON.stringify(item));

      },
      drop: function(e,list) {
        const pokeString = e.dataTransfer.getData('pokemon');
        const pokemon = JSON.parse(pokeString);
        this.validate(pokemon,list);
      },
      agregarEmit: function(e) {
        console.log(e);
        if(e.team==1){
          this.validate(e,this.team1);
        }else if(e.team==2){
          this.validate(e,this.team2);
        }
      },
      validate: function(pokemon,list){
        var rep = 0;
        for(var i = 0; i < list.length; i++) {
            console.log(list[i].name == pokemon.name);
            if (list[i].name == pokemon.name) {
                rep++;
                if(rep>1){
                  this.repetido=true;
                  return;
                  }else{
                    this.repetido=false;
                  }
                }
        }
        if(list.length>5){
          this.limite=true;
          return;
        }
        list.push(pokemon);
        this.saveAll();
      }
    },
    
    created() {
      this.currentUrl = this.apiUrl;
      this.fetchData();
    },
    mounted() {
      this.scrollTrigger();
      if (localStorage.getItem('pokemons')) {
        try {
          this.pokemons = JSON.parse(localStorage.getItem('pokemons'));
        } catch(e) {
          localStorage.removeItem('pokemons');
        }
      }
      if (localStorage.getItem('team1')) {
        try {
          this.team1 = JSON.parse(localStorage.getItem('team1'));
        } catch(e) {
          localStorage.removeItem('team1');
        }
      }
      if (localStorage.getItem('team2')) {
        try {
          this.team2 = JSON.parse(localStorage.getItem('team2'));
        } catch(e) {
          localStorage.removeItem('team2');
        }
      }
    }
  }
</script>