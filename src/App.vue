<template>
  <div id="app">
    <el-container>
      <el-main>
        <el-row type="flex" justify="center">
          <el-input placeholder="Search for Movie / TV Show" v-model="input" class="input-with-select">
            <el-button slot="append" icon="el-icon-search" @click="getData"></el-button>
          </el-input>
        </el-row>
        <transition name="el-fade-in-linear">

          <el-row class="desc" v-if="result.Title" align="middle" v-loading.fullscreen.lock="loading">
            <el-col :span="8">
              <div class="img-box" :style="{ backgroundImage: `url('${result.Poster}')` }">
              </div>
            </el-col>
            <el-col :span="16" style="padding-right: 15px;">
              <h1>{{ result.Title }}</h1>
              <div>
                <el-rate v-model="parsedRank" :max="10" disabled></el-rate>
                <div style="margin: 5px 0;">{{ result.imdbRating }} | {{ result.imdbVotes }} votes  </div>
              </div>
              <div class="subtext">{{ result.Runtime }} | {{ result.Genre }} | {{ result.Year }}</div>
              <hr>
              <div>{{ result.Plot }}</div>
              <div v-for="field in ['Actors','Director','Writer']" v-if="itemExists(result[field])">
                <h3>{{ field }}:</h3> {{ result[field] }}
              </div>
              <hr>
              <div v-for="field in ['Language', 'Type', 'Awards']" v-if="itemExists(result[field])">
                <h4>{{ field }}:</h4> {{ result[field] }}
              </div>
              <div v-if="itemExists(result.Website)">
                <h4>Website:</h4> <a :href="result.Website" target="_blank">{{ result.Website }}</a>
              </div>
            </el-col>
          </el-row>

          <el-row v-if="errorMessage" class="desc">
            <el-alert
              :title="errorMessage"
              type="warning"
              show-icon>
            </el-alert>
          </el-row>
        </transition>
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
        input: '',
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
      },
      itemExists(value) {
        return value !== 'NA';
      }
    },
    computed: {
      parsedRank() {
        return parseFloat(this.result.imdbRating);
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
    margin: 30px auto;
    padding: 0;
    border-radius: 10px;
    box-shadow: 0 0 10px #eee;
  }
  @media screen and (max-width: 600px) {
    .desc{
      width: 75vw !important;
    }
  }
  .el-input {
    width: 440px;
  }

  .img-box {
    height: 450px;
    margin: 0 15px;
    background-size: contain;
    background-position: center center;
    background-repeat: no-repeat;
  }

  h1, h2, h3, h4, h5, h6 {
    display: -webkit-inline-box;
    margin: 10px 0 5px;
  }

  hr {
    border: 0;
    height: 1px;
    background-image: linear-gradient(to right, rgba(0, 0, 0, 0), rgb(229, 221, 220), rgba(0, 0, 0, 0));
  }

  .subtext {
    font-size: 0.75rem;
  }
</style>
