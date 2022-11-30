<template>
    <div>
        <div class="" v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goTocityView(city)" />
        </div>

        <p class="" v-if="(savedCities.length === 0)"> No Location added. To start tracking a location, search in field above.</p>
    </div>
</template>
<script setup>
import { ref } from "@vue/reactivity";
import axios from "axios";
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue";
const router = useRouter();

const savedCities = ref([]);
const waeatherapikey = "fe6405e38ed14c95e6a6c300f7f75093";

const getCities =  async () => {
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'));

        const requests = [];


        savedCities.value.forEach((city) => {

            requests.push(axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.cords.lat}&lon=${city.cords.lng}&appid=${waeatherapikey}`));
        });

        const weatherData = await Promise.all(requests);

      weatherData.forEach((element, index) => {

        savedCities.value[index].weather = element.data;
        
      });



    }
   




}

const goTocityView = (city) => {
    router.push({name: 'cityview', params: {state: city.state, city: city.city}, query: {id: city.id, lat: city.cords.lat, lng: city.cords.lng}})

}

await getCities();

</script>