<template>
  <div>
    <div class="min-vh-100">
      <HeaderNavbar />
      <main class="container text-center mt-5 flex-column">
        <h1>Name: {{ pokemonData.name }}</h1>
        <h3>Capture Rate: {{ pokemonData.capture_rate }}</h3>
        <b-img
          center
          thumbnail
          :src="pokemonImage"
          alt="Image"
          class="my-4"
          width="300"
        ></b-img>
        <div class="fw-semibold fs-5">
          <p>Gender Rate: {{ pokemonData.gender_rate }}</p>
          <p>
            Has Gender Differences: {{ pokemonData.has_gender_differences }}
          </p>
          <p>Hatch Counter: {{ pokemonData.hatch_counter }}</p>
          <p>Order: {{ pokemonData.order }}</p>
        </div>
      </main>
    </div>

    <FooterComponent />
  </div>
</template>

<script>
import FooterComponent from "~/components/FooterComponent.vue";
import HeaderNavbar from "~/components/HeaderNavbar.vue";

const axios = require("axios").default;

export default {
  name: "DetailsPokemon",
  components: { HeaderNavbar, FooterComponent },
  data() {
    return {
      pokemonData: {},
      pokemonImage: "",
      propsImage: {
        blank: true,
        blankColor: "#777",
        class: "m1",
        width: "300px",
      },
      //pokemonId: this.$route.params.id
    };
  },
  mounted() {
    this.getDetailsPokemon();
  },
  methods: {
    getDetailsPokemon() {
      const endpoint = "https://beta.pokeapi.co/graphql/v1beta";

      const query = `
            query getPokemon {
                pokemon_v2_pokemonspecies_by_pk(id:  ${this.$route.params.id} ) {
                    name
                    capture_rate
                    base_happiness
                    gender_rate
                    has_gender_differences
                    hatch_counter
                    order
                },
                pokemon_v2_pokemonformsprites_by_pk(id: ${this.$route.params.id} ) {
                    sprites(path: "front_default")
                }
            } `;

      axios
        .post(endpoint, {
          query: query,
        })
        .then((response) => {
          this.pokemonData = response.data.data.pokemon_v2_pokemonspecies_by_pk;
          this.pokemonImage =
            response.data.data.pokemon_v2_pokemonformsprites_by_pk.sprites;
          console.log(this.pokemonData);
          console.log(this.pokemonImage);
        })
        .catch((error) => {
          console.log(error);
        })
        .finally(() => (this.loading = false));
    },
  },
};
</script>
