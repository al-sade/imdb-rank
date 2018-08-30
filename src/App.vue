<template>
  <div id="app">
    <el-container>
      <el-main>
        <el-row type="flex" justify="center">
          <el-input placeholder="Please input" v-model="input" class="input-with-select">
            <el-button slot="append" icon="el-icon-search" @click="getData"></el-button>
          </el-input>
        </el-row>
        <el-collapse-transition>

          <el-row class="grid-content desc" v-if="result.Title" v-loading.fullscreen.lock="loading">
            <el-col :span="8">
              <div class="img-box" :style="{ backgroundImage: `url('${result.Poster}')` }">
              </div>
            </el-col>
            <el-col :span="16">
              <h1>{{ result.Title }}</h1>
              <div>
                <el-rate v-model="result.imdbRating" :max="10" disabled></el-rate>
              </div>
              <div style="margin: 5px 0;">{{ result.imdbRating }} | {{ result.imdbVotes }}  </div>
              <div>{{ result.Runtime }} | {{ result.Genre }} | {{ result.Year }}</div>
              <hr>
              <div>{{ result.Plot }}</div>

              <div><h3>Actors:</h3> {{ result.Actors }}</div>
              <div><h3>Director:</h3> {{ result.Director }}</div>
              <div><h3>Writer:</h3> {{ result.Writer }}</div>
              <hr>
              <div><h4>Language:</h4> {{ result.Language }}</div>
              <div><h4>Type:</h4> {{ result.Type }}</div>
              <div><h4>Awards:</h4> {{ result.Awards }}</div>
              <div><h4>Website:</h4> <a :href="result.Website" target="_blank">{{ result.Website }}</a></div>
            </el-col>
          </el-row>
        </el-collapse-transition>
      </el-main>
    </el-container>
  </div>
</template>

<script>
  import axios from 'axios';

  export default {
    name: 'app',
    data() {
      return {
        input: 'titanic',
        value5: 6.7,
        baseUrl: 'http://www.omdbapi.com/',
        apiKey: '9e7c06f2',
        result: {},
        errorMessage: '',
        loading: false
      }
    },
    methods: {
      getData() {
        this.loading = true;

        let encodedName = encodeURIComponent(this.input);
        let endpoint = `${this.baseUrl}?apikey=${this.apiKey}&t=${encodedName}`;

        axios.get(endpoint)
          .then((response) => {
            if (response.data.Response === 'False') {
              this.errorMessage = response.data.Error;
              this.result = {};
            } else {
              this.result = response.data;
              this.errorMessage = '';
            }
            this.loading = false;
          })
          .catch((e) => {
            this.errorMessage = "Can't connect to server.";
            this.result = {};
            this.loading = false;
          });
      }
    }
  }
</script>

<style>
  #app {
    margin-top: 20vh;
    font-family: 'Roboto', sans-serif;
  }

  .desc {
    width: 60vw;
    margin: auto;
    margin-top: 30px;
    padding: 0px;
  }

  .box-header {
    background-color: #444;
    color: #FFF;
  }

  .el-input {
    width: 440px;
  }

  .img-box {
    min-height: 350px;
    margin: 0 15px;
    background-size: contain;
    background-position: center center;
    background-repeat: no-repeat;
  }

  .el-cols {
    padding-top: 1rem;
    padding-bottom: 1rem;
    min-height: 1px;
  }

  h1, h2, h3, h4, h5, h6 {
    display: -webkit-inline-box;
    margin: 10px 0px 5px;
  }

</style>
