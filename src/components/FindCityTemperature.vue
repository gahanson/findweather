<template>

<div id="findcitytemp">

        <form @submit.prevent="getCityWeather(count, cityname.name)">
            <input type="text" v-model="cityname.name" placeholder="Enter City Name"/>
            &nbsp;&nbsp;
            <button type="submit" class="btn btn-primary">Search Weather</button>
            <div>
                <label>Show Current</label>
                <div>
                    <input type="checkbox" id="temperature" value="temperature" v-model="checkedItems">
                    &nbsp;
                    <label for="temperature">Temp</label>
                    &nbsp;
                    <input type="checkbox" id="humidity" value="humidity" v-model="checkedItems">
                    &nbsp;
                    <label for="humidity">Humidity</label>
                    &nbsp;
                    <input type="checkbox" id="pressure" value="pressure" v-model="checkedItems">
                    &nbsp;
                    <label for="humidity">Pressure</label>
                </div>
            </div>
        </form>
        
        <div class="row">
            <table class="table">
            <thead>
                <tr>
                <th scope="col">search #</th>
                <th scope="col">City</th>
                <th v-if="checkedItems.includes('temperature')" scope="col">Temperature &#176C</th>
                <th v-if="checkedItems.includes('humidity')" scope="col">Humidity %</th>
                <th v-if="checkedItems.includes('pressure')" scope="col">Pressure (hPa)</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="search in allSearches" v-bind:key="search.id">
                <td scope="row">{{search.id}}</td>
                <td>{{search.city_name}}</td>
                <td v-if="checkedItems.includes('temperature')" >{{search.temp}}</td>
                <td v-if="checkedItems.includes('humidity')" >{{search.humidity}}</td>
                <td v-if="checkedItems.includes('pressure')" >{{search.pressure}}</td>
                </tr>
            </tbody>
            </table>

        </div><!--row-->
        <errmodal v-if="showErr" v-bind:headertext="headertext" v-bind:bodytext="bodytext" v-bind:footertext="footertext" @close="showErr = false"></errmodal>
        <!-- End of article modal -->
        <div class="loading" v-if="loading===true">Loading&#8230;</div>

</div>
  
</template>

<script>
import axios from "axios";
import modal from './StandardModal.vue';

export default {
    name: 'FindCityTemperature',
    components: {
        'errmodal':modal
    },
    data () {
        return {
        loading: false,
        headertext: 'Search Error',
        bodytext: '',
        footertext: 'Close',
        showErr: false,
        checkedItems: ['temperature'],
        cityname: {
            name: ''
        },
        allSearches: [],
        currentfindweather: {
            id: '',
            city_name: '',
            temp: ''
        },
        count: 0
        };
    },
    methods: {
        getCityWeather: function(id, cityName) {
            this.count = id += 1;
            var apiKey = process.env.VUE_APP_OPENWEATHER_API_KEY;
            var apiUrl = "https://api.openweathermap.org/data/2.5/weather?q="+cityName+"&units=metric&appid="+apiKey;
            this.loading = true;
            axios({method: "GET", "url": apiUrl }).then(result => {
                let newSearch = {
                    id: this.count,
                    city_name: cityName,
                    temp: JSON.stringify(result.data.main.temp),
                    humidity: JSON.stringify(result.data.main.humidity),
                    pressure: JSON.stringify(result.data.main.pressure)
                }
                this.allSearches.push(newSearch);
                this.loading = false;
                this.cityname.name = "";
            }, error => {
                this.loading = false;
                this.bodytext = error.response.data.message;
                this.cityname.name = "";
                this.showErr = true;
            });
        }//getCityWeather
    }//methods
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
