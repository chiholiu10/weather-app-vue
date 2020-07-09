<template>
  <div class="Search search-component">
    <form id="search-component" @submit="checkForm" class="form-component">
      <div class="form-inner-component">
        <div class="image-block">
          <img class="weater-icon" v-if="imageIcon !== null" :src="'https://www.weatherbit.io/static/img/icons/' + imageIcon + '.png'"/>
        </div>
        <div class="select">
          <select v-model.lazy="countryCode" @change="handleChange" class="select-field height-select-input select-width">
            <option v-for="(country, index) in checkCountryName"
              :key="index"
              :value="country.country_code"
              :selected="index === 0"
              :data-value="country.city_name"            
            >
              {{ country.country_code }}
            </option>
          </select>
        </div>

        <div class="input-component">
          <input class="input-field height-select-input" v-model.lazy="city" placeholder="Please enter your location...">
          <button type="submit" value="Submit" class="input-field-button">
            <svg width="18" height="20" viewBox="0 0 18 20" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path fill-rule="evenodd" clip-rule="evenodd" d="M15 7.5C15 11.6421 11.6422 15 7.5 15C3.35785 15 0 11.6421 0 7.5C0 3.35786 3.35785 0 7.5 0C11.6422 0 15 3.35786 15 7.5ZM13 7.5C13 10.5376 10.5375 13 7.5 13C4.46246 13 2 10.5376 2 7.5C2 4.46243 4.46246 2 7.5 2C10.5375 2 13 4.46243 13 7.5Z" fill="black"/>
              <path d="M14.5 14.3787C14.1095 13.9882 13.4763 13.9882 13.0858 14.3787L12.3787 15.0858C11.9882 15.4763 11.9882 16.1095 12.3787 16.5L14.8536 18.9749C15.4393 19.5607 16.3891 19.5607 16.9749 18.9749C17.5607 18.3891 17.5607 17.4393 16.9749 16.8535L14.5 14.3787Z" fill="black"/>
            </svg>
          </button>
        </div>

      </div>
    </form>

    <div v-for="(country, index) in info" :key="index">
      <div v-if="index < 7">

        <div v-if="index == 0" class="week-date-block">
          <div class="week-date date capitalize">{{ weekDate(country.datetime) }}</div>
          <div class="average-temperature">
            <div class="temperature-container">
              <div class="temperature temperature-week">{{ averageTemperature(country.min_temp, country.max_temp) }}
                <div class="celsius">°C</div>
              </div>
            </div>
          </div>
        </div>

        <div v-else class="day-of-weeks">
          <div class="date capitalize">{{ dayFullName(country.datetime) }}</div>
          <div class="temperature-container">
            <div class="temperature">{{ averageTemperature(country.min_temp, country.max_temp) }}
              <div class="celsius">°C</div>
            </div>
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
      imageIcon: null,
      city: '',
      cityList: [],
      countryCode: null,
      linearGradient: '',
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
      axios
        .get(`https://api.weatherbit.io/v2.0/forecast/daily?&city=${this.city}&country=${this.countryCode}&key=${this.apiKey}`)
        .then(response => {
          this.imageIcon = response.data.data[0].weather.icon
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
      this.linearGradient = averageTemperature
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

<style>
/* body {
  background: ;
} */


.form-component {
  max-width: 650px;
  min-width: 300px;
  height: 92px;
  left: 404px;
  margin-top: 194px;
  margin-left: auto;
  margin-right: auto;
  background-color: #FFFFFF;
  border-radius: 16px;
  box-shadow: 0px 2px 10px rgba(8, 21, 62, 0.15);
}
.image-block {
  width: 50px;
}
.weater-icon {
  width: 50px;
  height: auto;
  left: -20px;
}
.height-select-input {
  padding: 15px
}
.select-width {
  width: 95px;
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
  position: relative;
}
.temperature-container {
  color: #FFFFFF;
  font-size: 24px;
  display: flex;
  position: relative;
  justify-content: center;
}
.temperature-week {
  font-size: 120px;
}
.temperature-week .celsius {
  font-size: 24px;
  top: 10px;
}
.temperature {
  position: relative;
}
.input-field {
  border: 1px solid rgba(8, 21, 62, 0.05);
  width: 400px;
  color: #08153E;
  border-radius: 6px;
}
.select-field {
  font-size: 14px;
  font-weight: 600;
}
.select-field,
.input-field {
  margin-left: 10px;
  border-radius: 6px;
  border: 1px solid rgba(8, 21, 62, 0.05);
}
.input-field:focus,
.select-field:focus,
.input-field-button:focus {
  outline: none;
}
.input-field-button {
  position: absolute;
  background-color: transparent;
  border: none;
  height: 100%;
  right: 5px;
}
.input-field-button {
  opacity: 0.6;
}
.input-field:hover + .input-field-button {
  opacity: 0.6;
}
.input-field:focus + .input-field-button {
  opacity: 1;
}
.input-field-button:active + .input-field-button {
  opacity: 1;
}
.select-field {
  margin-left: 10px;
}
.select {
  position: relative;
}
.select:after {
  content: "";
  position: absolute;
  right: 8px;
  top: 45%;
  width: 0;
  height: 0;
  border-left: 5px solid transparent;
  border-right: 5px solid transparent;
  border-top: 6px solid #000000;
  pointer-events: none;
  opacity: 0.6;
}
select {
  /* for Firefox */
  -moz-appearance: none;
  /* for Chrome */
  -webkit-appearance: none;
}
select::-ms-expand {
  display: none;
}
.input-component {
  display: flex;
  position: relative;
}
svg {
  right: 0;
}
.average-temperature {
  font-size: 100px;
  margin-top: 15px;
  height: 170px;
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
  right: -22px;
  top: -6px;
  position: absolute;
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
@media (max-width: 767px) {
  .image-block {
    display: none;
  }
  .day-of-weeks {
    width: 100%;
  }
  .input-field {
    width: 180px;
  }
  .form-component {
    margin-top: 0;
    border-radius: 0;
    max-width: default;
  }
}

</style>
