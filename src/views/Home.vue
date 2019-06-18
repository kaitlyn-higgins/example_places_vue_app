<template>
  <div class="home">

    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>

    <h2>New Place</h2>
    <div>
      Name: <input type="text" v-model="newPlaceName">
      Address: <input type="text" v-model="newPlaceAddress">
      <button v-on:click="createPlace()">Create Place</button>
    </div>

    <h1>{{ message }}</h1>
    <div v-for="place in places">
      <h2>Name: {{ place.name }}</h2>
      <button v-on:click="showPlace(place)">See Address</button>
      <div v-if="currentPlace === place">
        <p>address: {{ place.address }}</p>
        <div>
          Name: <input type="text" v-model="place.name">
          Address: <input type="text" v-model="place.address">
          <button v-on:click="updatePlace(place)">Update Place</button><br>
          <button v-on:click="destroyPlace(place)">Destroy Place</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
</style>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      message: "Places",
      errors: [],
      places: [],
      currentPlace: {},
      newPlaceName: "",
      newPlaceAddress: ""
    };
  },
  created: function() {
    axios.get("/api/places").then(response => {
      this.places = response.data;
    });
  },
  methods: {
    createPlace: function() {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress
      };
      axios.post("/api/places/", params).then(response => {
        this.places.push(response.data);
        console.log(response.data);
        this.newPlaceName = "";
        this.newPlaceAddress = "";
      }).catch(error => {
        this.errors = error.response.data.errors;
      });
    },
    showPlace: function(place) {
      if (this.currentPlace === place) {
        this.currentPlace = {};
      } else {
        this.currentPlace = place;
      }
    },
    updatePlace: function(place) {
      var params = {
        name: place.name,
        address: place.address
      };
      axios.patch("/api/places/" + place.id, params).then(response => {
        this.currentPlace = {};
      }).catch(error => {
        this.errors = error.response.data.errors;
      });
    },
    destroyPlace: function(place) {
      axios.delete("/api/places/" + place.id).then(response => {
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    }
  }
};
</script>