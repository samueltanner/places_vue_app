<template>
  <div class="home">
    <h1>Oh, the Places You'll Go</h1>
    <ul>
      <li v-for="error in errors" v-bind:key="error">Errors: {{ error }}</li>
    </ul>
    <div>
      <input type="text" v-model="placeName" placeholder="Place Name" />
      <input type="text" v-model="placeAddress" placeholder="Place Address" />
      <br />
      <button v-on:click="createPlace()">Add a Destination</button>
    </div>
    <div v-for="place in places" v-bind:key="place.id">
      <h2>{{ place.name }}</h2>
      <!-- <p>{{ place.address }}</p> -->
      <button v-on:click="showPlace(place)">Show Location</button>
    </div>

    <div>
      <dialog id="place-info">
        <form>
          <h1>Place Info</h1>
          <h3>{{ currentPlace.name }}</h3>
          <p>{{ currentPlace.address }}</p>
          <input type="text" v-model="currentPlace.name" placeholder="New Name" />
          <input type="text" v-model="currentPlace.address" placeholder="New Address" />
          <br />
          <br />
          <button v-on:click="updatePlace(currentPlace)">Update</button>
          <button v-on:click="destroyPlace(currentPlace)">Delete</button>
          <button>Close</button>
        </form>
      </dialog>
    </div>
  </div>
</template>

<style></style>

<script>
import axios from "axios";

export default {
  data: function () {
    return {
      errors: [],
      places: [],
      placeName: "",
      placeAddress: "",
      currentPlace: {},
      currentPlaceName: "",
      currentPlaceAddress: "",
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("/api/places").then((response) => {
        this.places = response.data;
        console.log(response.data);
      });
    },
    createPlace: function () {
      var params = {
        name: this.placeName,
        address: this.placeAddress,
      };
      axios
        .post("/api/places", params)
        .then((response) => {
          console.log("place was created", response.data);
          this.places.push(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-info").showModal();
    },
    updatePlace: function (place) {
      var params = {
        name: place.name,
        address: place.address,
      };
      axios
        .patch("/api/places/" + place.id, params)
        .then((response) => {
          console.log("place was updated", response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    destroyPlace: function (place) {
      axios
        .delete("/api/places/" + place.id)
        .then((response) => {
          console.log("place was deleted", response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
          var index = this.places.indexOf(place);
          this.places.splice(index, 1);
        });
    },
  },
};
</script>
