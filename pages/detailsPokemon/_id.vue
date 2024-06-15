<template>
    <div>
        <header-navbar />
        <div class="container text-center mb-5">
            <h1> Name: {{ pokemon.name }}</h1>
            <h3>Origin: {{ pokemon.origin.name }}</h3>
            <b-img thumbnail fluid :src="pokemon.image" alt="Image" class="my-4"></b-img>
            <div class="fw-semibold fs-5">
                <p>Location: {{ pokemon.location.name }}</p>
                <p>Gender: {{ pokemon.gender }}</p>
                <p>Species: {{ pokemon.species }}</p>
                <p>Status: {{ pokemon.status }}</p>
                <p v-if="pokemon.type">Type: {{ pokemon.type }}</p>
            </div>
        </div>
        <footer-component />
    </div>
</template>


<script>

const axios = require('axios').default;

export default {
    name: 'DetailsPokemon',
    data() {
        return {
            pokemon: {},
            mainProps: { blank: true, blankColor: '#777', width: 75, height: 75, class: 'm1' }
        }
    },
    methods: {
        getDetailspokemon() {
            const endpoint = "https://beta.pokeapi.co/graphql/v1beta";

            const query = `
            query getPokemons {
                pokemon_v2_pokemonspecies(where: {id: {_eq: ${this.$route.params.id}}}) {
                    name
                    capture_rate
                    base_happiness
                    gender_rate
                    has_gender_differences
                    hatch_counter
                    order
                }
            } `;

            axios
                .post(endpoint, {
                    query: query,
                })
                .then((response) => {
                    this.pokemon = response.data.data.pokemon;
                    console.log(this.pokemon)
                })
                .catch((error) => {
                    console.log(error);
                })
                .finally(() => (this.loading = false));
        }
    },
    mounted() {
        this.getDetailspokemon();
    }
}

</script>
