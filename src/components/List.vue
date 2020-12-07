<template>
    <div class="container my-5 " >
        <div class="row">
          <div class="col-sm-6">
            <div class="card">
              <div class="card-body pb-0">
                <h5 class="card-title d-inline">Equipo 1</h5>
                <hr>
                  <i class="fas fa-plus-square d-inline mr-2"></i>
                  <p class="card-text d-inline">Arrastra tus pokemons aqui</p>
              </div>
              <div class="card-body" id="board-1" @dragover.prevent @drop.prevent="drop">
              </div>

            </div>
          </div>
          <div class="col-sm-6">
            <div class="card">
              <div class="card-body pb-0">
                <h5 class="card-title d-inline">Equipo 2</h5>
                <hr>
                  <i class="fas fa-plus-square d-inline mr-2"></i>
                  <p class="card-text d-inline">Arrastra tus pokemons aqui</p>
              </div>
              <div class="card-body" id="board-2" @dragover.prevent @drop.prevent="drop">
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
              @dragstart="dragStart"
              @dragover.stop
              draggable="true">
                <div class="card mb-3 mx-auto" style="width: 18rem;">
                  <img :src="imageUrl + pokemon.id + '.png'" width="200" height="200" alt="" class="mx-auto" draggable="false">
                  <div class="card-body">
                      <h5 class="card-title">{{ pokemon.name }}</h5>
                      <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
                      <div>
                        <b-button v-b-modal="'modal'+pokemon.id">Agregar</b-button>
                        <b-modal :id="'modal'+pokemon.id" :title="pokemon.name">
                          <Detail :pokemonUrl="pokemon.url"
                            :imageUrl="imageUrl + pokemon.id + '.png'"/>
                        </b-modal>
                      </div>
                  </div>
                </div><slot/>
            </div>
            <div id="scroll-trigger" ref="infinitescrolltrigger">
            <i class="fas fa-spinner fa-spin"></i>
            </div>
        </div>
    </div>
    
</template>
<script>
import Detail from './Detail';

  export default {
    props: [
      'imageUrl',
      'apiUrl'
    ],
    data: () => {
      return {
        pokemons: [],
        nextUrl: '',
        currentUrl: ''
      }
    },
    components:{
      Detail
    },
    methods: {
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
      setPokemonUrl(url) {
        this.$emit('setPokemonUrl', url);
      },
      dragStart: e => {
        const target = e.target;
        
        e.dataTransfer.setData('card_id',target.id);

      },
      drop: e => {
        const card_id = e.dataTransfer.getData('card_id');
        
        const card = document.getElementById(card_id);
        
        card.style.display = "block";
        
        e.target.appendChild(card);
      }
    },
    created() {
      this.currentUrl = this.apiUrl;
      this.fetchData();
    },
    mounted() {
      this.scrollTrigger();
    }
  }
</script>