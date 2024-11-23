<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">Pesquisar filmes no IMDB</h1>
    <div class="input-group mb-3">
      <input
        type="text"
        v-model="query"
        class="form-control"
        placeholder="Insira o nome do filme"
        aria-label="Search for a movie"
      />
      <button class="btn btn-primary" @click="searchMovies">
        <i class="ri-search-line"></i> Pesquisar
      </button>
    </div>
    <div v-if="errorMessage" class="alert alert-danger">{{ errorMessage }}</div>
    <div id="movieResults" class="row">
      <div v-for="movie in movies" :key="movie.imdbID" class="col-md-4 mb-3">
        <div class="card h-100">
          <img
            :src="movie.Poster"
            class="card-img-top"
            :alt="movie.Title"
            v-if="movie.Poster"
          />
          <div class="card-body">
            <h5 class="card-title">{{ movie.Title }}</h5>
            <p class="card-text"><strong>Year:</strong> {{ movie.Year }}</p>
            <a
              :href="'https://www.imdb.com/title/' + movie.imdbID"
              target="_blank"
              class="btn btn-primary"
              >Ver Filme</a
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      query: "",
      movies: [],
      errorMessage: "",
      defaultMovies: [
        {
          Title: "Avatar",
          Year: "2009",
          Poster:
            "https://upload.wikimedia.org/wikipedia/en/d/d6/Avatar_%282009_film%29_poster.jpg",
          imdbID: "tt0499549",
        },
        {
          Title: "Transformers",
          Year: "2007",
          Poster:
            "https://upload.wikimedia.org/wikipedia/en/6/66/Transformers07.jpg",
          imdbID: "tt0418279",
        },
        {
          Title: "The Avengers",
          Year: "2012",
          Poster:
            "https://upload.wikimedia.org/wikipedia/en/f/f9/TheAvengers2012Poster.jpg",
          imdbID: "tt0848228",
        },
      ],
    };
  },
  methods: {
    async searchMovies() {
      if (this.query.trim() === "") {
        this.errorMessage = "Por favor, insira o título de um filme!";
        return;
      }
      this.errorMessage = ""; // Limpa a mensagem de erro
      const apiKey = "3PHr5qGq337arQu1TXdLmT:68kY6OGU3D1xD9i7Oxzepp";

      try {
        const response = await fetch(
          `https://api.collectapi.com/imdb/imdbSearchByName?query=${this.query}`,
          {
            method: "GET",
            headers: {
              authorization: `apikey ${apiKey}`,
              "content-type": "application/json",
            },
          }
        );

        const data = await response.json();

        if (data.success) {
          this.movies = data.result;
        } else {
          this.errorMessage = "Filme não encontrado!";
          this.movies = [];
        }
      } catch (error) {
        this.errorMessage = "Problemas com a Internet. Tente novamente!";
        console.error(error);
      }
    },
  },
  mounted() {
    this.movies = this.defaultMovies;
  },
};
</script>

<style scoped></style>
