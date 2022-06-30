<template>
  <div class="row">
    <div class="col-sm-12 form-group">
      <label>Please search an address</label>
      <input
          class="form-control"
          type="text"
          v-model="address_input"
          @keyup="get_addresses_debounced"
      />
    </div>
  </div>
</template>

<script>
import _ from "lodash";
import axios from "axios";

export default {
  name: 'App',
  components: {

  },
  data(){
    return{
      address_input: null,
      get_addresses_debounced: null
    }
  },
  methods:{
    async get_addresses() {
      this.loading = true;
      let response = [];
      try {
        // Comment this line and uncomment the next one to make local api calls
        response = await axios.get(
            `https://nominatim.openstreetmap.org/search?city=${this.address_input}&format=json&addressdetails=0`
        );
        // response = await axios.get("./index.json");
      } catch (error) {
        console.error(error);
      }
      console.log(response.data);

      this.polygon(response.data[0].lat,response.data[0].lon);

    },
    polygon(lat, lon){
      axios.post( 'https://script.kdm1.ru/skystrim/api.php',
          {lat:lat, lon:lon},
      ).then((resp)=>{
        console.log(resp.data);

      }).catch(function(error){
        console.log(error);
      });
    }
  },
  created() {
    this.get_addresses_debounced = _.debounce(this.get_addresses, 1000);
  },
}
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
