<template>
  <div class="home">
    <h1>{{ message }}</h1>
   
    <div>
      <h3>Make new place:</h3>
      <!-- Absolutely could not figure out how to get the errors to display on the view page. -->
      <ul>
        <li v-for="error in errors">{{ error }}</li>
      </ul>
      <h5>Name: </h5><input type="text" v-model="newPlaceName">
      <h5>Address: </h5><input type="text" v-model="newPlaceAddress"><br><br>
      <button v-on:click="createPlace()">Create</button>
    </div>

    <h2>Places</h2>
    <div v-for="place in places">
      <h4>{{ place.name }}</h4>
      <p>{{ place.address }}</p>
      <button v-on:click="showPlace(place)">More Info</button>
    </div>

    <dialog id="place-details">
      <form method="dialog">
        <h2>Place Info</h2>
        <ul>
          <li v-for="error in errors">{{ error }}</li>
        </ul>
        <p>Name: <input type="text" v-model="currentPlace.name"></p>
        <p>Address: <input type="text" v-model="currentPlace.address"></p>
        <button v-on:click="updatePlace(currentPlace)">Edit</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button><br>
        <button>Close</button>
      </form>
    </dialog>

  </div>
</template>

<style>
</style>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      message: "Welcome to Places!",
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
      errors: []
    };
  },
  created: function() {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/api/places").then( response => {
        console.log(response.data);
        this.places = response.data;
      });
    },
    createPlace: function () {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };

      axios.post("/api/places", params).then(response => {
        console.log("Success", response.data);
        this.places.push(response.data);
        this.newPlaceName = "";
        this.newPlaceAddress = "";
      }).catch( error => {
        console.log(error.response.data.errors);
        this.errors = error.response.data.errors; 
      });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function (place) {
      var params = {
        name: place.name,
        address: place.address
      };
      axios.patch(`/api/places/${place.id}`, params).then( response => {
        console.log("Success", response.data);
      }).catch( error => {
        console.log(error.response.data.errors);
      });
    },
    destroyPlace: function (place) {
      axios.delete(`/api/places/${place.id}`).then( response => {
        console.log("Success", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    }

  }
};
</script>