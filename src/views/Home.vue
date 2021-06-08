<template>
  <div class="home">
    Name:
    <input type="text" v-model="newPlaceName" />
    <br />
    Address:
    <input type="text" v-model="newPlaceAddress" />
    <button v-on:click="createPlace()">Create Place</button>

    <div v-for="place in places" v-bind:key="place.id">
      <h3>Name: {{ place.name }}</h3>
      <button v-on:click="showPlace(place)">More Info</button>
      <p>Address: {{ place.address }}</p>
    </div>
    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Info</h1>
        <p>
          Name:
          <input type="text" v-model="currentPlace.name" />
        </p>
        <p>
          Address:
          <input type="text" v-model="currentPlace.address" />
        </p>
        <button v-on:click="updatePlace()">Update Place</button>
        <button v-on:click="destroyPlace()">Destroy Place</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style></style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      places: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {},
    };
  },
  created: function () {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function () {
      axios.get("http://localhost:3000/places").then((response) => {
        console.log(response.data);
        this.places = response.data;
      });
    },

    createPlace: function () {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress,
      };
      axios
        .post("http://localhost:3000/places", params)
        .then((response) => {
          console.log("Success!", response.data);
          this.places.unshift(response.data);
          this.newPlaceName = "";
          this.newPlaceAddress = "";
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    showPlace: function (place) {
      console.log(place);
      this.currentPlace = place;
      document.querySelector("#place-details").showModal();
    },
    updatePlace: function () {
      console.log(this.currentPlace);
      var params = {
        name: this.currentPlace.name,
        address: this.currentPlace.address,
      };
      axios
        .patch(`http://localhost:3000/places/${this.currentPlace.id}`, params)
        .then((response) => {
          console.log("Success!", response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    destroyPlace: function () {
      axios.delete(`http://localhost:3000/places/${this.currentPlace.id}`).then((response) => {
        console.log("Successfully Deleted!", response.data);
        var index = this.places.indexOf(this.currentPlace);
        this.places.splice(index, 1);
      });
    },
  },
};
</script>
