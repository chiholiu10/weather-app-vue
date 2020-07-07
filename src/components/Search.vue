
<template>
  <div class="Search">
      <div>Weather App</div>
      <form id="search-component" @submit="checkForm" class="form-component">
        <select v-model="countryCode" @change="handleChange">
          <option v-for="(country, index) in checkCountryName" :key="index" :value="country.country_code" :selected="index === 0">
              {{ country.country_code }}
          </option>
        </select>
         <div v-if="country !== undefined">{{ country.city_name }}</div>
        <input class="input-field" v-model="city" placeholder="Please enter your location...">

        <input
          type="submit"
          value="Submit"
        >
      </form>

      <div v-for="(country, index) in info" :key="index">
        <div v-if="index <= 7">
          <div v-if="index == 0" class="entire-week">
            <div class="week-date">
              {{ weekDate(country.datetime) }}
            </div>
            <div class="average-temperature">
              {{ averageTemperature(country.min_temp, country.max_temp) + ' °C' }}
            </div>
          </div>

          <div v-else>
            {{ dayFullName(country.datetime) }}
            {{ averageTemperature(country.min_temp, country.max_temp) + ' °C' }}
          </div>
          
        </div>
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
      city: '',
      cityList: [],
      countryCode: null,
      errors: []
    }
  },
  mounted () {
    axios.get('https://gist.githubusercontent.com/chiholiu/9243c14cd7f310c0866947414496ad99/raw/8f97404080b3812477004793e2318552fc876aa2/cities.json')
      .then(response => {
          this.cityList = response.data
      })
  },
  computed: {
    checkCountryName () {
      if(this.city.length < 1) return;
      return this.cityList.filter((cityName) => {
        return cityName.city_name.toLowerCase().match(this.city.toLowerCase())
      })
    }
  },
  methods: {
    checkForm (e) {
      e.preventDefault()
      // .get(`https://api.weatherbit.io/v2.0/forecast/daily?&city=${this.city}&country=${this.countryCode}&key=${this.apiKey}`)
      axios
        .get(`https://api.weatherbit.io/v2.0/forecast/daily?&city=amsterdam&country=NL&key=${this.apiKey}`)
        .then(response => {
          console.log(response.data.data)
          this.info = response.data.data
        })
        .catch(err => {
          console.log(err)
        })
    },
    weekDate (value) {
      const entireWeek = moment(value).format('MMMM D -') + moment(value).add(7, 'days').format('D YYYY')
      return entireWeek
    },
    dayFullName (date) {
      const getFullName = moment(date).format('dddd')
      return getFullName
    },
    averageTemperature (minTemperature, maxTemperature) {
      const averageTemperature = Math.floor((minTemperature + maxTemperature) / 2)
      return averageTemperature
    },
    handleChange (e) {
       if(e.target.options.selectedIndex > -1) {
          console.log(e.target.options[[e.target.options.selectedIndex]])
       }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.form-component {
  max-width: 632px;
  height: 92px;
  left: 404px;
  top: 194px;
  
}
.input-field {
  border: 1px solid rgba(8, 21, 62, 0.05);
}
.average-temperature {
  font-size: 100px;
}
</style>
