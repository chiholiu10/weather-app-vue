<template>
  <div class="Search search-component">
    <form id="search-component" @submit="checkForm" class="form-component">
      <div class="form-inner-component">
        <select v-model="countryCode" @change="handleChange">
          <option v-for="(country, index) in checkCountryName"
            :key="index"
            :value="country.country_code"
            :selected="index === 0"
            :data-value="country.city_name"
          >
            {{ country.country_code }}
          </option>
        </select>

        <div class="input-component">
          <input class="input-field input-float" v-model="city" placeholder="Please enter your location...">
          <button type="submit" value="Submit" class="input-field-button input-float"></button>
        </div>

      </div>
    </form>

    <div v-for="(country, index) in info" :key="index">
      <div v-if="index < 7">

        <div v-if="index == 0" class="week-date-block">
          <div class="week-date date capitalize">{{ weekDate(country.datetime) }}</div>
          <div class="average-temperature">
            <div class="temperature">
              {{ averageTemperature(country.min_temp, country.max_temp) }}
              <span class="celsius">°C</span>
            </div>
          </div>
        </div>

        <div v-else class="day-of-weeks">
          <div class="date capitalize">{{ dayFullName(country.datetime) }}</div>
          <div class="temperature">
            {{ averageTemperature(country.min_temp, country.max_temp)}}
            <span class="celsius">°C</span>
          </div>
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
      if (this.city.length < 1) return
      return this.cityList.filter((cityName) => {
        return cityName.city_name.toLowerCase().match(this.city.toLowerCase())
      })
    }
  },
  methods: {
    checkForm (e) {
      e.preventDefault()
      console.log(this.city, this.countryCode)
      axios
        .get(`https://api.weatherbit.io/v2.0/forecast/daily?&city=${this.city}&country=${this.countryCode}&key=${this.apiKey}`)
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
      if (e.target.options.selectedIndex > -1) {
        this.city = e.target.options[[e.target.options.selectedIndex]].getAttribute('data-value')
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
  margin-left: auto;
  margin-right: auto;
  background-color: #FFFFFF;
  border-radius: 16px;
  box-shadow: 0px 2px 10px rgba(8, 21, 62, 0.15);
}
.capitalize {
  text-transform: uppercase;
}
.form-inner-component {
  display: flex;
  flex-direction: row;     /* make main axis horizontal (default setting) */
  justify-content: center; /* center items horizontally, in this case */
  align-items: center;     /* center items vertically, in this case */
  height: 100%;
}
.temperature {
  color: #FFFFFF;
  font-size: 24px;
}
.input-field {
  border: 1px solid rgba(8, 21, 62, 0.05);
  width: 425px;

  color: #08153E;
  border-radius: 6px;
}
.input-field:focus {
  outline: none;
}
.input-component {
  content: "";
  clear: both;
  display: table;
}
.input-float {
  float: left;
}
.average-temperature {
  font-size: 100px;
  margin-top: 25px;
  height: 100px;
  font-weight: 600;
  font-size: 120px;
  line-height: 120px;
  color: #FFFFFF;
}
.week-date-block {
  padding-top: 50px;
}
.date {
  color: #08153E;
  letter-spacing: 0.06em;
  opacity: 0.6;
}
.day-of-weeks {
  float: left;
  width: calc(100% / 6);
}
.celsius {
  font-size: 14px;
  line-height: 24px;
  top: 0;
}
.day-of-weeks.date {
  float: left;
  width: 100%;
}
.search-component {
  max-width: 800px;
  margin-left: auto;
  margin-right: auto;
}
</style>
