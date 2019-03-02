<template>
  <div id="app">
    <v-app>
      <div>
        <v-alert
          v-model="alert"
          dismissible
          type="success"
        >
          This is a success alert that is closable.
        </v-alert>
      </div>
      <div>
        <div v-if="showprogress" class="text-xs-center">
          <v-progress-circular            
            indeterminate
            color="primary"
          ></v-progress-circular>
        </div>
      </div>
      <div>
        <v-layout align-start justify-center>
          <v-flex xs12>
            <v-card>
              <v-container grid-list-md>
                <v-layout row wrap>
                  <v-flex v-for="card in cards" :key="card.title" xs2>
                    <v-card>
                      <v-img :src="card.src">
                        <v-container>
                          <v-layout >
                            <v-flex flexbox>
                              <span class="headline white--text" v-text="card.title"></span>
                            </v-flex>
                          </v-layout>
                        </v-container>
                      </v-img>
                    </v-card>
                  </v-flex>
                </v-layout>
              </v-container>
            </v-card>
          </v-flex>
        </v-layout>
      </div>
    </v-app>
  </div>
</template>

<script>
  import Vue from 'vue'
  import Vuetify from 'vuetify'
  import axios from 'axios';

  Vue.use(Vuetify)

  export default {
    name: 'app',
    components: {
    },
    data () {
      return {
        alert: false,
        showprogress: true,
        cards: []
      }
    },
    created() {

      var bodystring = "<REQUEST><LOGIN authenticationkey='ae0e9ff415394f82a60c7c0cff97fa88'/><QUERY objecttype='Camera'><FILTER><AND><EQ name='Active' value='true' /><GT name='PhotoTime' value='$dateadd(-0.00:15:00)' /></AND></FILTER></QUERY></REQUEST>";

      axios.post('http://api.trafikinfo.trafikverket.se/v1.3/data.json', bodystring, { headers: { 'Content-type': 'text/xml' } })
      .then(response => {

        this.showprogress = false;

        //debugger;
        var cameras = [];
        for (var i = 0; i < response.data.RESPONSE.RESULT[0].Camera.length; i++) {
          cameras.push({title: response.data.RESPONSE.RESULT[0].Camera[i].Id, src: response.data.RESPONSE.RESULT[0].Camera[i].PhotoUrl});
          console.log(response.data.RESPONSE.RESULT[0].Camera[i].PhotoUrl)
        }

        this.cards = cameras;
      })
      .catch(e => {
        console.log("ERROR");
      })
    }
  }

</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }
</style>
