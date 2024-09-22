<template>
  <v-container>
    <v-img
      src="@/assets/Rick-and-Morty.png"
      max-width="500px"
      class="mx-auto"></v-img>

    <div class="text-center">
      <v-select
        v-model="episodioSelecionado"
        :items="episodios"
        label="Procure um episódio aqui"
        item-title="name"
        item-value="id"
        @update:modelValue="buscarPersonagens"
      ></v-select>
    </div>

    <v-row class="mx-auto">
      <v-col v-for="character in characters" :key="character.id" cols="12" md="4">
        <v-card height="385px" width="300px" hover class="align-center">
          <v-img :src="character.image" height="170px" class="mt-4"></v-img>
          <v-card-text class="text">
            <v-card-title style="color: #88e23b;"><strong>{{ character.name }}</strong></v-card-title>
            <v-card-subtitle><v-icon icon="mdi-circle" size="xs-small" :color="getStatusColor(character.status)"/> {{ character.status }} || {{ character.species }}</v-card-subtitle>
            <v-card-text class=""><strong>First seen in: </strong> {{ character.origin.name }}</v-card-text>
            <v-card-text class="pt-0"><strong>Last seen in: </strong> {{ character.location.name }}</v-card-text>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>

  </v-container>
</template>

<script>
  import axios from "axios";

  export default {
    data() {
      return {
        episodioSelecionado: '',
        episodios: [],
        characters: [],
      };
    },

    mounted() {
      this.buscarEpisodios();
    },

    methods: {
      buscarEpisodios() {
        axios
          .get("https://rickandmortyapi.com/api/episode")
          .then((response) => {
            this.episodios = response.data.results.map((episode) => {
              return {
                id: episode.id,
                name: `Episode ${episode.id}: ${episode.name}`,
              };
            });
          })
          .catch((error) => {
            console.error("Erro ao pegar episódios:", error);
          });
      },

      buscarPersonagens(episodioSelecionado) {
        const url = `https://rickandmortyapi.com/api/episode/${episodioSelecionado}`;

        axios
          .get(url)
          .then((response) => {
            const characterUrls = response.data.characters;

            Promise.all(characterUrls.map((url) => axios.get(url)))
              .then((responses) => {
                this.characters = responses.map((res) => res.data);
              })
              .catch((error) => {
                console.error("Erro ao buscar personagens:", error);
              });
          })
          .catch((error) => {
            console.error("Erro ao pegar episódio:", error);
          });
      },

      getStatusColor(status) {
        return status === 'Alive' ? 'success' : status === 'Dead' ? 'error' : 'grey';
      },
    },
  };
</script>

