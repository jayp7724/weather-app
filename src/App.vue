<template>
  <v-app>
    <v-app-bar app flat color="rgba(0, 0, 0, 0)">
      <v-app-bar-nav-icon color="white" @click="drawer = !drawer"></v-app-bar-nav-icon>

      <v-toolbar-title class="text-h6 white--text pl-0">
        Weather App
      </v-toolbar-title>

      <v-spacer></v-spacer>

      <v-btn color="white" icon>
        <v-icon>mdi-magnify</v-icon>
      </v-btn>
    </v-app-bar>
    <v-navigation-drawer
        v-model="drawer"
        absolute
        temporary
      >
      <v-list
        nav
        dense
      >
        <v-list-item-group
          v-model="group"
          active-class="deep-purple--text text--accent-4"
        >
          <v-list-item>
            <v-list-item-icon>
              <v-icon>mdi-home</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Home</v-list-item-title>
          </v-list-item>

          <v-list-item>
            <v-list-item-icon>
              <v-icon>mdi-account</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Account</v-list-item-title>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>

    <v-main>
      <v-carousel :show-arrows="false" v-model="model" height="100vh">
        <v-carousel-item v-for="(city) in cities" :key="city">
          <v-sheet :color="getRandomColor()" height="100%" tile>
            <v-row class="fill-height" align="center" justify="center">
              <!-- <div class="text-h2">Slide {{ i + 1 }}</div> -->
              <div class="d-flex flex-column">
                <v-card-text>
                  <h1 class="text-h3" align="center" color="white">{{currentCity}}</h1>
                  <p class="text-h6" align="center" color="white">{{title}}</p>
                  <div align="center">
                    <img :src="icon" alt="">
                  </div>
                  <p class="text-h1" align="center">{{actual}}</p>
                </v-card-text>
              </div>
            </v-row>
          </v-sheet>
        </v-carousel-item>
      </v-carousel>
    </v-main>
  </v-app>
</template>

<script>

import gql from 'graphql-tag'

export default {
  name: "App",

  mounted(){
    this.fetchCities();
  },

  components: {},

  data: () => ({
    model: 0,
    //colors: ["primary", "green", "yellow darken-2", "red", "orange"],
    cities: [],
    drawer: false,
    group: null,
    weather: {},
  }),

  methods: {

    fetchCities(){
      this.cities = ["Barrie","Antarctica","Montego Bay","London","Jerusalem"];
    },

    getRandomInt(max = 128){
      return Math.floor(Math.random() * Math.floor(max));
    },

    getRandomColor(){
      return `rgb(${this.getRandomInt()},${this.getRandomInt()},${this.getRandomInt()})`
    },
  },

  computed: {
    currentCity(){
    return this.weather?.name;
    },

    title(){
    return this.weather?.weather?.summary?.title;
    },

    actual(){
    const temp = this.weather?.weather?.temperature?.actual;
      return temp ? Math.round(temp) : "-";
    },

    icon(){
    const iconCode = this.weather?.weather?.summary?.icon;
    return iconCode ? "http://openweathermap.org/img/wn/"+iconCode+"@4x.png" : "";
    }
  },

  apollo: {
  // Simple query that will update the 'hello' vue property

  weather: {

    query: gql `
      query getWeather($city: String!){
         weather: getCityByName(name: $city, config:{units:metric}) {
              name
              weather{
                summary{
                  title
                  icon
                },
                temperature{
                  actual
                }
              }

            }
      }
    `,
    variables(){
      return{
        city: this.cities[this.model],
      };
    },
  },
},
};
</script>

<style>
.v-main {
  margin-top: -57px;
}
</style>
