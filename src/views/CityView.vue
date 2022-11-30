<template>
    <div>
     <Suspense>
        <AsyncCityView />
        <template #fallback>
       
               <CityViewSeleleton />
         
        </template>
     </Suspense>
    </div>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue';
import CityViewSeleleton from '../components/CityViewSeleleton.vue';



</script>