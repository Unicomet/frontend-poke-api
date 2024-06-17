<template>
    <div class="container">
        <b-row class="d-flex justify-content-center align-items-center w-75 mx-auto">
            <label :for="'search'" class="w-auto mr-2">Find By Name:</label>

            <b-form-input id="search" v-model="inputSearch" @input="findPokemonsByPage()" class="w-auto"></b-form-input>
            <b-button class="ms-4 w-auto" @click="findPokemonsByPage()">Search</b-button>
        </b-row>
        <div class="row mt-5 mx-0">
            <div class="col-12 px-0">
                <div class="row mx-5 ">
                    <div v-for="pokemon in pokemons" :key="pokemon.id" class="card m-3" style="width: 18rem; ">

                        <img v-if="pokemon.sprites" :src="pokemon.sprites" class="card-img-top" alt="...">

                        <div class="card-body ">
                            <h3 class="card-title">{{ pokemon.pokemon_v2_pokemon.name }}</h3>
                            <a>
                                <NuxtLink :to="`/detailsPokemon/${pokemon.pokemon_id}`" class="btn btn-primary">
                                    See Details
                                </NuxtLink>
                            </a>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class=" w-25 mx-auto my-5">
            <b-pagination v-model="currentPage" :total-rows="totalItems" :per-page="itemsPerPage"
                aria-controls="pokemons-list" @change="findPokemonsByPage"></b-pagination>
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
            itemsPerPage: 21,
        }
    },
    mounted() {
        this.findPokemonsByPage();
    },
    methods: {
        findPokemonsByPage(page = 1) {
            const endpoint = "https://beta.pokeapi.co/graphql/v1beta";
            this.currentPage = page;

            console.log(this.inputSearch);
            const queryFindPokemonsByPage = `
                query samplePokeAPIquery {
                    pokemon_v2_pokemonsprites(limit: ${this.itemsPerPage}, offset: ${21 * (this.currentPage - 1)}, where: {pokemon_v2_pokemon: {name: {_iregex: "${this.inputSearch}"}}}) {
                        pokemon_id
                        pokemon_v2_pokemon {
                            name
                        }
                        sprites(path: "other.home.front_default")
                    }
                    pokemon_v2_pokemon_aggregate(where: {name:{_iregex:"${this.inputSearch}"}}) {
                        aggregate {
                            count
                        }
                    }                        
                }
            `;


            axios
                .post(endpoint, {
                    query: queryFindPokemonsByPage
                })
                .then((response) => {
                    console.log(response)
                    this.pokemons = response.data.data.pokemon_v2_pokemonsprites;
                    this.totalItems = response.data.data.pokemon_v2_pokemon_aggregate.aggregate.count;
                    console.log(this.totalItems);
                    //this.totalItems = response.data.data.pokemons.info.count
                })
                .catch((error) => {
                    console.log(error);
                })
                .finally(() => (this.loading = false));
        },
    }
}
</script>
