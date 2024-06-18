<template>
    <div class="">
        <b-form inline class="justify-content-center">
            <label class="mr-sm-2" for="inline-form-custom-select-pref">Find By</label>
            <b-form-select id="input-filter" class="mb-2 mr-sm-2 mb-sm-0" :options="options" v-model="selectedFilter"
                @input="getMaxLimit()"></b-form-select>
            <label class="sr-only" for="inline-form-input-username">Username</label>
            <b-input-group class="mb-2 mr-sm-2 mb-sm-0">
                <b-form-input id="search" v-model="inputSearch" @input="findPokemonsByPage()"
                    placeholder="Write..."></b-form-input>
                <b-form-input v-show="selectedFilter != 'name'" id="range" v-model="inputSearch" type="range" min="0"
                    max="maxLimit" @input="findPokemonsByPage()"></b-form-input>
            </b-input-group>

            <b-button variant="primary" class="ms-4 w-auto" @click="findPokemonsByPage()">Search</b-button>
        </b-form>

        <div class="row mt-5 justify-content-center w-100">
            <div v-for="pokemon in pokemons" :key="pokemon.id" class="card m-3" style="width: 18rem; ">

                <img v-if="pokemon.pokemon_v2_pokemonsprites[0].sprites"
                    :src="pokemon.pokemon_v2_pokemonsprites[0].sprites" class="card-img-top" alt="...">

                <div class="card-body ">
                    <h3 class="card-title">{{ pokemon.name }}</h3>
                    <a>
                        <NuxtLink :to="`/detailsPokemon/${pokemon.id}`" class="btn btn-primary">
                            See Details
                        </NuxtLink>
                    </a>
                </div>
            </div>
        </div>
        <div class=" w-25 mx-auto my-5">
            <b-pagination v-model="currentPage" :total-rows="totalItems" :per-page="itemsPerPage"
                aria-controls="pokemons-list"></b-pagination>
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
            itemsPerPage: 20,
            selectedFilter: "name",
            maxLimit: 0,
            maxLimits: null,
            apiURL: "https://beta.pokeapi.co/graphql/v1beta",
            options: [
                { value: "name", text: 'Name' },
                { value: 'height', text: 'Height', },
                { value: 'weight', text: 'Weight', }]
        }
    },
    mounted() {
        this.findPokemonsByPage();
        this.getMaxLimit();
    },
    methods: {
        getMaxLimit() {
            if (this.maxLimits === null) {
                const queryGetMax = `
                    query getMaxLimit {
                        pokemon_v2_pokemon_aggregate {
                            aggregate {
                                max {
                                    height
                                    weight
                                }
                            }
                        }
                    }
                `;

                axios.post(
                    this.apiURL, {
                    query: queryGetMax
                },
                ).then((response) => {
                    console.log("Max Limits");
                    this.maxLimits = response.data.data.pokemon_v2_pokemon_aggregate.aggregate.max;
                    console.log(this.maxLimits)

                }).catch((error) => {
                    console.log(error);
                }).finally(() => {
                    this.loading = false;
                })
            }
            //Asignamos el limite maximo segun el filtro seleccionado
            if (this.selectedFilter === 'weight') {
                this.maxLimit = this.maxLimits.weight;
            } else if (this.selectedFilter === 'height') {
                this.maxLimit = this.maxLimits.height;
            }
            console.log("Max limit: " + this.maxLimit)
        },
        findPokemonsByPage(page = 1) {
            this.currentPage = page;
            let offset = this.itemsPerPage * (this.currentPage - 1);
            console.log("Input: " + this.inputSearch);
            console.log("Filter: " + this.selectedFilter);

            let whereParam = "";

            switch (this.selectedFilter) {
                case 'name':
                    whereParam = `name: {_regex: "${this.inputSearch}"}`;
                    break;
                case 'height':
                    whereParam = `height: {_eq: ${this.inputSearch}}`;
                    break;
                case 'weight':
                    whereParam = `weight: {_eq: ${this.inputSearch}}`;
                    break;
                default:
            }

            const queryFindPokemonsByPage = `
                query samplePokeAPIquery {
                    pokemon_v2_pokemon(limit: ${this.itemsPerPage}, offset: ${offset}, where: { ${whereParam} }) {
                        id
                        name
                        pokemon_v2_pokemonsprites{
                        sprites(path: "other.official-artwork.front_default")
                        }
                    }
                    pokemon_v2_pokemon_aggregate(where: { ${whereParam} }) {
                        aggregate {
                            count
                        }
                    } 
                }
            `;


            axios
                .post(this.apiURL, {
                    query: queryFindPokemonsByPage
                })
                .then((response) => {
                    this.pokemons = response.data.data.pokemon_v2_pokemon;
                    this.totalItems = response.data.data.pokemon_v2_pokemon_aggregate.aggregate.count;
                    if (this.selectedFilter != 'name') {
                        if (this.selectedFilter === 'height')
                            this.maxLimit = response.data.data.pokemon_v2_pokemon_aggregate.aggregate.max.height;
                        else if (this.selectedFilter === 'weight')
                            this.maxLimit = response.data.data.pokemon_v2_pokemon_aggregate.aggregate.max.weight;
                    }
                    console.log("Total items: " + this.totalItems);
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
