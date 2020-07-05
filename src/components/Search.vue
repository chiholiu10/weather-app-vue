
<template>
  <div class="Search">
      <div>Weather App</div>
      <form id="search-component" @submit="checkForm">
        <input v-model="city" placeholder="Please enter your location...">
  
        <input
          type="submit"
          value="Submit"
        >
      </form>
        <div v-for="(country, index) in info" :key="index"> 
          <template v-if="index == 0">
             {{ country.datetime }}
          </template>
          <template v-else>
            {{ country.datetime ||  moment("dddd") }}
          </template>
          {{ Math.floor((country.min_temp + country.max_temp) / 2) }}
        </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Search',
  data () {
    return {
      apiKey: 'daa13c2413cf4c4c92eca3f2ae204eda',
      info: null,
      city: null,
      errors: []
    }
  },
  methods: {
    checkForm (e) {
      e.preventDefault()
      console.log(this.city);
      axios
        .get(`https://api.weatherbit.io/v2.0/forecast/daily?&city=${this.city}&country=US&key=${this.apiKey}`)
        .then(response => {
        console.log(response.data)
        this.info = response.data.data
      })
      .catch(err => {
        console.log(err)
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
