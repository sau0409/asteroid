<template>
  <div id="app">
    <AsteroidGrid
      :asteroids="asteroids"
      header="Near Earth Objects"
      @remove="remove()"
    ></AsteroidGrid>
  </div>
</template>

<script>
import AsteroidGrid from "./components/AsteroidGrid";
import axios from "axios";

export default {
  name: "App",
  components: {
    AsteroidGrid,
  },
  data() {
    return {
      asteroids: [],
    };
  },
  methods: {
    fetchAsteroids() {
      const apikey = "WmoBkz1PKexFyh9bdb1Y6R8dXblEIm05DcRqCPag";
      const url =
        "https://api.nasa.gov/neo/rest/v1/neo/browse?api_key=" + apikey;
      axios.get(url).then((res) => {
        console.log(res.data);
        this.asteroids = res.data.near_earth_objects.slice(0, 20);
      });
    },
    remove(index) {
      this.asteroids.splice(index, 1);
    },
  },
  created() {
    this.fetchAsteroids();
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
