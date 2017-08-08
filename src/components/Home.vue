<template>
  <div>
    <v-card>
      <v-card-text class="text-xs-center">
        <h2 class="white--text">
          <v-icon left large>movie</v-icon> Find Movie Information</h2>
        <p style="font-style: italic;">"If I could only search movie, series, etc. information from one place..."</p>
        <p>Updates on the way! Stay tuned! :)</p>
        <v-layout row>
          <v-flex xs12>
            <v-text-field v-model="searchField" label="Enter a Movie or TV Show Here..." prepend-icon="search" hint="press enter after typing"
              @keyup.enter="search(true)" :append-icon="hasSearch ? 'clear_all' : ''" :append-icon-cb="clearSearch"></v-text-field>
          </v-flex>
        </v-layout>
      </v-card-text>
    </v-card>
    <div class="py-5 text-xs-center" v-show="noImageFound">
      <img src="https://res4.nbstatic.in/static/images/no-result.svg" alt="Movie not found" width="300">
    </div>
    <div v-infinite-scroll="loadMore" infinite-scroll-disabled="busy" infinite-scroll-immediate-check="true">
      <v-card class="margin-card-top" v-for="(m,i) in list" :key="i">
        <v-card-text>
          <v-layout row wrap>
            <v-flex xs12 sm12 md3 lg43 xl3>
              <div v-if="m.poster == 'N/A'">
                <img src="../assets/no-movie.png" alt="no-image">
              </div>
              <div v-else><img :src="m.poster" :alt="m.Title" class="img-responsive"></div>
            </v-flex>
            <v-flex xs12 sm12 md9 lg9 xl9 class="margin-info-top">
              <div class="px-3">
                <h3 class="white--text">{{ m.title }}</h3>
                <span v-if="m.type == 'movie'">Movie</span>
                <span v-else-if="m.type == 'series'">Series</span>
                <span v-else-if="m.type == 'episode'">Episode</span> | {{ m.imdbRating }}/10 <br> Release Date: {{ m.released
                }} <br> Genres: {{ m.genre }} <br><br>
                <p v-if="m.plot == 'N/A'">No plot available.</p>
                <p v-else>{{ m.plot }}</p>
                <p>IMDbID: {{ m.imdbID }}</p>
                <div class="moreBtn" @click="viewMore(m)">
                  <v-btn class="pink white--text">View More
                    <v-icon right>more_vert</v-icon>
                  </v-btn>
                </div>
              </div>
            </v-flex>
          </v-layout>
        </v-card-text>
      </v-card>
    </div>
    <div class="text-xs-center py-5" v-show="loader">
      <v-progress-circular indeterminate v-bind:size="70" class="white--text"></v-progress-circular>
    </div>
    <!-- modal -->
    <v-dialog v-model="movieDialog" persistent width="700" scrollable>
      <v-card>
        <v-card-title class="pink white--text">
          <div class="headline">
            <v-btn icon @click="movieDialog = false">
              <v-icon>arrow_back</v-icon>
            </v-btn> {{movie.title}}</div>
        </v-card-title>
        <v-card-text style="height: 500px;">
          <span v-if="movie.type == 'movie'">Movie</span>
          <span v-else-if="movie.type == 'series'">Series</span>
          <span v-else-if="movie.type == 'episode'">Episode</span> | {{ movie.imdbRating }}/10 |
          Release Date: {{ movie.released }}
          <br> Genres: {{ movie.genre }} <br><br>
          <p>Directed by: {{ movie.director }}</p>
          <p v-if="movie.plot == 'N/A'">No plot available.</p>
          <p v-else>{{ movie.plot }}</p>
          <p>IMDbID: {{ movie.imdbID }}</p>
          <p>Runtime: {{ movie.runtime }}</p>
          <p>Writer: {{ movie.writer }}</p>
          <p>Actors: {{ movie.actors }}</p>
          <p>Rated: {{ movie.rated }}</p>
          <p>Awards: {{ movie.awards }}</p>
          <p>Website: <a :href="movie.website">{{ movie.website }}</a></p>
          <p>Metascore: {{ movie.metascore }}</p>
          <p>IMDb Votes: {{ movie.imdbVotes }}</p>
          <p>IMDb Rating: {{ movie.imdbRating }}</p>
          <p>Production: {{ movie.production }}</p>
        </v-card-text>
      </v-card>
    </v-dialog>
    <!-- modal -->
    <!-- snackbar -->
    <v-snackbar
      :timeout="3000"
      :top="true"
      :right="true"
      v-model="snackbar"
    >
     loading movies/shows...
      <v-btn flat class="pink--text" @click.native="snackbar = false">Close</v-btn>
    </v-snackbar>
    <!-- snackbar -->
  </div>
</template>



<script>
import axios from 'axios';
export default {
  name: 'homepage',
  data () {
    return {
      list: [],
      movie: {},
      searchField: null,
      hasSearch: false,
      endpoint: "http://www.omdbapi.com",
      page: 1,
      loader: false,
      movieDialog: false,
      noImageFound: false,
      totalResults: 0,
      snackbar: false,
      apiKey: ""
    }
  },
  methods: {
    search(clear) {
      if(!this.searchField) return false;
      if(clear) this.list = [];
      this.loader = true;
      this.snackbar = true;
      this.noImageFound = false;
      const _self = this;
      axios.get(`${this.endpoint}/?s=${this.searchField}&apikey=${this.apiKey}&page=${this.page}&plot=full`).then(movieResponse => {
       // console.log(movieResponse.data);
        const data = movieResponse.data;
        _self.totalResults = data.totalResults;
        if(data.Response == "True") {
          data.Search.forEach((movie) => {
            axios.get(`${this.endpoint}/?i=${movie.imdbID}&apikey=${this.apiKey}&plot=full`).then(imdbResponse => {
              const imdbData = imdbResponse.data;
              _self.list.push({
                title: imdbData.Title,
                year: imdbData.Year,
                rated: imdbData.Rated,
                released: imdbData.Released,
                runtime: imdbData.Runtime,
                genre: imdbData.Genre,
                director: imdbData.Director,
                writer: imdbData.Writer,
                actors: imdbData.Actors,
                plot: imdbData.Plot,
                awards: imdbData.Awards,
                poster: imdbData.Poster,
                type: imdbData.Type,
                imdbRating: imdbData.imdbRating,
                metascore: imdbData.Metascore,
                imdbVotes: imdbData.imdbVotes,
                imdbID: imdbData.imdbID,
                website: imdbData.Website,
                ratings: imdbData.Ratings,
                production: imdbData.Production
              });
            }).catch(imdbError => {
              //console.log(imdbError)
              //_self.noImageFound = true;
             //_self.loader = false;
            })
          })
          _self.loader = false;
        } else {
          if(_self.list.length == 0) {
            _self.noImageFound = true;
            _self.loader = false;
          }
          _self.loader = false;
        }
      }).catch(error => {
        _self.loader = false;
       // _self.noImageFound = true;
      })
    },
    loadMore() {
      const rounded = Math.round(this.totalResults);
      if(this.list.length > 0) {
          if(rounded == (this.page - 1)) {
          this.noImageFound = false;
          this.loader = false;
          return false;
        }
        this.loader = true;
        this.page += 1;
        this.search(false);
      }
    },
    clearSearch() {
      this.searchField = null;
      this.hasSearch = false;
    },
    viewMore(movie) {
      this.movie = movie;
      this.movieDialog = true;
    }
  },
  watch: {
    searchField: function(val) {
      if(val) {
        this.hasSearch = true;
      } else {
        this.hasSearch = false;
      }
    }
  }
}
</script>

<style scoped>
  .margin-card-top {
    margin-top: 18px;
  }

  .margin-info-top {
    margin-top: 12px;
  }

  .img-responsive {
    width: 100%;
    height: auto;
  }

  .moreBtn {
    margin-top: 80px;
  }
</style>
