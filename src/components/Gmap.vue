<template>
  <div class="Map" />
</template>

<script>
import gmapsInit from "../utils/gmaps";
import MarkerClusterer from "@google/markerclusterer";

export default {
  name: "Gmap",
  data() {
    return {
      users: [],
      coords: []
    };
  },
  async mounted() {
    try {
      // await fetch("http://localhost:8000/api/v2/pages/?type=customers.CustomerPage&fields=formatted_address")
      //   .then(res => res.json())
      //   .then(res => (this.users = res.items));
      const google = await gmapsInit();
      const geocoder = new google.maps.Geocoder();
      const map = new google.maps.Map(this.$el, {
        zoom: 7,
        center: { lat: 53.825, lng: -1.57 }
      });
      const markers = this.users.map(user => {
        geocoder.geocode(
          { address: user.formatted_address },
          (results, status) => {
            if (status !== "OK" || !results[0]) {
              throw new Error(status);
            }
            var coords = results[0].geometry.location;
            var infowindow = new google.maps.InfoWindow({
              content: user.title
            });
            var marker = new google.maps.Marker({
              position: coords,
              map: map
            });
            marker.addListener("click", function() {
              infowindow.open(map, marker);
            });
            marker.setMap(map);
          }
        );
      });
      new MarkerClusterer(map, markers, {
        imagePath:
          "https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m"
      });
    } catch (error) {
      console.error(error);
    }
  }
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
}

.Map {
  width: 100vw;
  height: 90vh;
}
</style>
