<template>
    <div class="container">
        <b-row class="d-flex justify-content-center align-items-center w-75 mx-auto">
            <label :for="'search'" class="w-auto mr-2">Find By Name:</label>
            <b-form-input :id="'search'" class="w-auto" v-model="inputSearch" v-on:input="findByName"></b-form-input>
            <b-button class="ms-4 w-auto" @click="findByName">Search</b-button>
        </b-row>
        <div class="row mt-5 mx-0">
            <div class="col-12 px-0">
                <div class="row mx-5 ">
                    <div v-for="pokemon in pokemons" v-bind:key="pokemon.id" class="card m-3" style="width: 18rem; ">

                        <img v-if="pokemon.sprites" :src="pokemon.sprites" class="card-img-top" alt="...">

                        <div class="card-body ">
                            <h3 class="card-title">{{ pokemon.pokemon_v2_pokemon.name }}</h3>
                            <!-- <p>{{ pokemon.status }}</p>
                            <p>{{ pokemon.species }}</p>
                            <p>{{ pokemon.gender }}</p>
                            <p v-if="pokemon.type">{{ pokemon.type }}</p>
                            <p v-else>unknown</p> -->
                            <router-link class="btn btn-primary" :to="{ name: 'detailsPokemon' }">See
                                Details</router-link>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class=" w-25 mx-auto my-5">
            <b-pagination v-model="currentPage" :total-rows="totalItems" :per-page="itemsPerPage"
                aria-controls="pokemons-list" v-on:change="findByName"></b-pagination>
        </div>
    </div>
</template>



<script>
const axios = require('axios').default;


export default {
    name: 'ListItems',
    data() {
        return {
            pokemons: {},
            inputSearch: "",
            currentPage: 1,
            totalItems: 0,
            itemsPerPage: 20
        }
    },
    mounted() {
        this.getpokemons();
    },
    methods: {
        getpokemons() {
            const endpoint = "https://beta.pokeapi.co/graphql/v1beta";

            const queryGetpokemons = `query samplePokeAPIquery {
                pokemon_v2_pokemonsprites {
                    pokemon_id
                    pokemon_v2_pokemon {
                    name      
                    }
                    sprites(path: "other.home.front_default")
                }
                } `;
            axios
                .post(endpoint, {
                    query: queryGetpokemons
                })
                .then((response) => {
                    console.log(response)
                    console.log("Sprite: " + response.data.data.pokemon_v2_pokemonsprites[0].sprites)
                    this.pokemons = response.data.data.pokemon_v2_pokemonsprites;
                    //this.totalItems = response.data.data.pokemons.info.count
                })
                .catch((error) => {
                    console.log(error);
                })
                .finally(() => (this.loading = false));
        },
        findByName(inputSearch) {
            console.log(inputSearch)
            this.inputSearch = inputSearch;
            const endpoint = "https://rickandmortyapi.com/graphql";
            const queryFindByName = `
                        query findByName {
                            pokemons (page: 1 ,filter: { name: "${this.inputSearch}" }){
                                info {
                                    count
                                    pages
                                }
                                results {
                                    id
                                    image
                                    name
                                    status
                                    species
                                    type
                                    gender
                                }
                            }
                        } `;
            axios
                .post(endpoint, {
                    query: queryFindByName,
                })
                .then((response) => {
                    this.pokemons = response.data.data.pokemons.results;
                    this.totalItems = response.data.data.pokemons.info.count;
                    console.log(this.pokemons);
                })
                .catch((error) => {
                    console.log(error);
                })
                .finally(() => (this.loading = false));
        }
        ,
    }
}
</script>
