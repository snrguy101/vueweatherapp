<template>
<main class="container text-white">
<div class="pt-4 mb-8 relative">
    <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="Seach City or State" class="py-2 px-1 w-full bg-transparent bottom-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">


<ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="mapboxresults">
    <p class=" text-white p-3 bg-red-600 mt-3" v-if="searcherror">Sorry, Something Went Wrong please try again</p>
    <li v-else class="py-2 cursor-pointer" v-for="searchResult in mapboxresults" :key="searchResult.id" @click="previewCity(searchResult)">{{searchResult.place_name}}</li>
</ul>
</div>

<div class="flex flex-col gap-4">
    <Suspense>
        <CityList />
        <template #fallback>
           <CityCardSkeleton />
        </template>
    </Suspense>
</div>


</main>
</template>
<script setup>
import { ref } from "@vue/reactivity";
import axios from "axios";
import { useRouter } from "vue-router";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";
import CityList from '../components/CityList.vue';
  const searchQuery = ref("");
  const queryTimeout = ref(null);
  const mapboxresults = ref(null);
  const searcherror = ref(null);
  const router = useRouter();
  const mapboxapikey = 'pk.eyJ1IjoibXJmYXNoIiwiYSI6ImNsYjJwOWJyNDAzeWwzcG91ZmV3b202bXgifQ.gjKDDy1FgfK-TcsGqmISXQ';
  const previewCity = (searchresult) => {
    console.log(searchresult)

    const [city, state] =  searchresult.place_name.split(",");
    router.push({name: 'cityview', params: {state: state.replaceAll(" ", ""), city: city}, query: {lat: searchresult.geometry.coordinates[1], lng: searchresult.geometry.coordinates[0], preview: true}})
  }
  const getSearchResults = () => {
   clearTimeout(queryTimeout.value)
  queryTimeout.value =  setTimeout( async () => {
    if (searchQuery.value  !== "") {
        try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxapikey}&types=place`);
        mapboxresults.value = result.data.features;
        console.log(mapboxresults.value);
        return;
        } catch (error) {
            searcherror.value = true;
        }
       
    }
    mapboxresults.value = null;
  }, 300);
  }
</script>