
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
          <template v-if="index <= 10">
            <template v-if="index == 0">
              {{ weekDate(country.datetime) }}
            </template>
            <template v-else>
              {{ dayFullName(country.datetime) }}
            </template>
            {{ averageTemperature(country.min_temp, country.max_temp) + ' °C' }}
          </template>
        </div>
  </div>
</template>

<script>
import axios from 'axios'
import moment from 'moment'

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
    },
    weekDate (value) {
      const entireWeek = moment(value).format("MMMM D -") + moment(value).add(7, 'days').format("D YYYY")
      return entireWeek;
    },
    dayFullName (date) {
      const getFullName = moment(date).format('dddd');
      return getFullName;
    },
    averageTemperature (minTemperature, maxTemperature) {
      const averageTemperature = Math.floor((minTemperature + maxTemperature) / 2)
      return averageTemperature;
    }

              
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
