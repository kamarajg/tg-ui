<template>
  <div>
    <div class="container">
      <h2 v-if="placeInfo" class="display-4">
        Attractions in {{ placeInfo.name }}
      </h2>
      <h2 v-else>
        <p>No place information available.</p>
      </h2>
      <div class="row" v-if="placeInfo"> 
        <div class="attractions col-lg-4">
          <ul class="list-group">
            <li
              v-for="(attraction, index) in attractionsToDisplay"
              :key="index"
              class="list-group-item"
              @click="getPlaceDetails(attraction.xid)"
            >
              {{ attraction.name }}
            </li>
          </ul>
          <br />
          <div class="pagination" v-if="attractionsToDisplay.length > 0">
            <button
              @click="prevPage"
              :disabled="currentPage === 1"
              class="btn btn-primary btn-sm me-xxl-3"
            >
              Previous
            </button>
            <button
              @click="nextPage"
              :disabled="currentPage === totalPages"
              class="btn btn-primary btn-sm me-xxl-3"
            >
              Next
            </button>
          </div>
          <br />
          <br />
        </div>
        <div class="placeDetails col-lg-8">
          <PlaceDetails v-if="placeDetails" :placeDetails="placeDetails" />
          <br />
          <br />
        </div>
      </div>
    </div>
  </div>
</template>



<script>
import axios from "axios";
import PlaceDetails from "./PlaceDetails.vue";

const apiKey = "5ae2e3f221c38a28845f05b677b72cac7311c902fee4a2e8ca68391a";

export default {
  components: {
    PlaceDetails,
  },
  props: {
    placeId: String,
  },
  data() {
    return {
      placeInfo: null,
      attractions: [],
      placeDetails: null,
      currentPage: 1,
      itemsPerPage: 5,
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.attractions.length / this.itemsPerPage);
    },
    attractionsToDisplay() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.attractions.slice(startIndex, endIndex);
    },
    pages() {
      return Array.from({ length: this.totalPages }, (_, index) => index + 1);
    },
  },
  watch: {
    placeId: "fetchPlaceInfo",
  },
  mounted() {
    this.fetchPlaceInfo();
  },
  methods: {
    async fetchPlaceInfo() {
      const response = await axios.get(
        `https://api.opentripmap.com/0.1/en/places/geoname?apikey=${apiKey}&name=${this.placeId}`
        // https://api.opentripmap.com/0.1/en/places/geoname?apikey=5ae2e3f221c38a28845f05b61de3e1c96cc4be1e55e26b5e8166170a&name=london
      );
      this.placeInfo = response.data;
      if (response.status === 200 && response.data.status !== "NOT_FOUND") {
        const res = await axios.get(
          `https://api.opentripmap.com/0.1/en/places/radius?apikey=${apiKey}&radius=1000&limit=500&offset=0&lon=${this.placeInfo.lon}&lat=${this.placeInfo.lat}&rate=2&format=json`
        );

        if (res.status === 200) {
          this.attractions = res.data;
        }
      } 
      else if (response.status === 429) {
        console.error(response.data.error);
      }
      else {
        console.error("Error fetching place info");
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage += 1;
      }
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage -= 1;
      }
    },
    goToPage(page) {
      this.currentPage = page;
    },
    async getPlaceDetails(xid) {
      const placeDetailsRes = await axios.get(
        `https://api.opentripmap.com/0.1/en/places/xid/${xid}?apikey=${apiKey}`
      );

      if (placeDetailsRes.status === 200) {
        this.placeDetails = placeDetailsRes.data;
      }
    },
  },
};
</script>
