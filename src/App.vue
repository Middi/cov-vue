<template>
  <div id="app">
    <Gmap />
  </div>
</template>

<script>
import Gmap from "./components/Gmap";

export default {
  name: "App",
  data() {
    return {
      latest: {},
      all: [],
      us: [],
      china: [],
      unitedKingdom: [],
      canada: [],
      countries: [],
      some: []
    };
  },
  components: {
    Gmap
  },
  mounted() {
    fetch(
      "https://coronavirus-tracker-api.herokuapp.com/v2/locations?source=jhu"
    )
      .then(res => res.json())
      .then(data => {
        this.all = data.locations;
        this.latest = data.latest;

        // var a = [];

        // data.locations.forEach(item => {
        //   var index = a.findIndex(x => x.country == "US");
        //   // here you can check specific property for an object whether it exist in your array or not

        //   if (index === -1) {
        //     var newItem = {
        //       country: item.country,
        //       confirmed: item.latest.confirmed,
        //       deaths: item.latest.deaths,
        //       recovered: item.latest.recovered
        //     };
        //     a.push(newItem);
        //   } else {
        //     a[index] = {
        //       country: a[index].country,
        //       confirmed: a[index].confirmed,
        //       deaths: a[index].deaths,
        //       recovered: a[index].recovered,
        //       dupe: "eded"
        //     };
        //   }

        //   this.some = a;
        // });

        let getCountryList = [];
        for (var i = 0; i < data.locations.length; i++) {
          getCountryList.push(data.locations[i].country);
        }

        function removeDuplicates(countries) {
          let s = new Set(countries);
          let it = s.values();
          return Array.from(it);
        }

        this.countries = removeDuplicates(getCountryList);

        this.removeDupe("US", data);

        this.removeDupe("China", data);

        this.removeDupe("United Kingdom", data);

        this.removeDupe("Canada", data);
      });
  },
  methods: {
    removeDupe(country, data) {
      function camalize(str) {
        return str
          .toLowerCase()
          .replace(/[^a-zA-Z0-9]+(.)/g, (m, chr) => chr.toUpperCase());
      }
      var locations = data.locations;
      var countryArr = [];
      locations.forEach(item => {
        if (item.country == country) {
          countryArr.push(item);
        }
      });
      var state = camalize(country);
      this[state] = countryArr;
    }
  }
};
</script>

<style lang="scss">
#app {
}
</style>
