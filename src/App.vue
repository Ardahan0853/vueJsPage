<script setup>

import { ref } from 'vue'





let apiData = ref([])
  
const url = `https://gateway.marvel.com:443/v1/public/comics?ts=1686493625925&apikey=3c5fa7ce02d711e408a509b29042a16c&hash=c8d2259335322d8429eaac737a5c3c2f`

fetch(url)
    .then(response => response.json())
    .then(data => {
        apiData.value = data.data.results 
        apiData.value.forEach(item => item['isFavorite'] = false)
        apiData.value.forEach(item => item['showCreators'] = false)
        apiData.value.forEach(item => item['showDesc'] = false)

        console.log(apiData);
    })  
   
    
    

</script>

<template>
  <div>
    
    <nav>
        
        <!-- <ComicsCreator :datas="apiData" @buttonClicked="showAlert"/> -->
        <RouterLink to="/"></RouterLink>
        <RouterView :dataID="apiData"/>
    </nav>
  </div>
</template>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Rubik:wght@300;400;500;700&display=swap');

* {
  font-family: "Rubik", sans-serif;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.container {
  
  margin:  auto;
}

</style>
