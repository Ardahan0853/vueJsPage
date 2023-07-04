<script setup>
import { ref } from 'vue'

// Dinamik data için ref methodunu kullanıyoruz.

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

// let date = new Date()

// console.log(date.getTime()) API uzerinen veri almak icin kullandigim function. Kodun üzerinde bir etkisi yok.

    // Yıldız svg'sini const a atıyoruz bu sayede bir daha bu kadar uzun bir text ile uğraşmak zorunda kalmıyoruz
    const svg = '<svg height="200px" width="200px" version="1.1" id="Capa_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" viewBox="0 0 53.867 53.867" xml:space="preserve" fill="#000000"><g id="SVGRepo_bgCarrier" stroke-width="0"></g><g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g><g id="SVGRepo_iconCarrier"> <polygon style="fill:#EFCE4A;" points="26.934,1.318 35.256,18.182 53.867,20.887 40.4,34.013 43.579,52.549 26.934,43.798 10.288,52.549 13.467,34.013 0,20.887 18.611,18.182 "></polygon> </g></svg>'

    function renderFavorite() {
        const favNumberDOM = document.getElementById('Favorite-dom')
        favNumberDOM.innerHtml = `<h1 id="Favorite-dom">Favorites ${svg} ${favNumber}`
    }
    let currentCreator 
    function renderCreator(name , role , iD){
        
        currentCreator = document.getElementById(iD)
        if(name && role){
            currentCreator.innerHTML += `<h2 class="creators" v-if="data.showCreators"><span>Name:</span> ${name}   <span>Role:</span>  ${role} </h2>`
        }else{
            currentCreator.innerHTML = ''
        }
        
    }
    function renderDesc(desc , iD){
        currentCreator = document.getElementById(iD)
        if(desc){
            currentCreator.innerHTML = `<p class="description" v-if="data.showDesc">  ${desc}</p>`
        }else{
            currentCreator.innerHTML = ''
        }
    }
   
   let favNumber = 0

   let nextPageDataArr  
    // Send functionlari buttonlara atadığım functionlar. Buttonlardan veri alımını bu functionlar üzerinden gerçekleştiriyoruz.
   function sendItem(dataID , dataArr){
    console.log(dataID)
    nextPageDataArr = dataArr
    console.log(nextPageDataArr)
    dataArr.isFavorite = !dataArr.isFavorite
    if(dataArr.isFavorite){
        favNumber = favNumber + 1
        renderFavorite()
    }if(!dataArr.isFavorite){
        favNumber = favNumber - 1
        renderFavorite()
    }
    console.log(favNumber)
   }
   function sendCreators(dataID, dataArr){
    console.log(dataID)
    dataArr.showCreators = !dataArr.showCreators
    apiData.forEach(item =>{
        if(item.id === dataID){
            console.log(dataArr.showCreators)
            if(dataArr.showCreators){
            console.log(dataArr.creators.items)
            dataArr.creators.items.forEach(item => renderCreator(item.name , item.role , dataID))
            }else{
            currentCreator.innerHTML = ''
            renderCreator("", "",dataID)
        }
        }
    })
   }
   function sendDesc(dataID, dataArr){
    console.log(dataID)
    dataArr.showDesc = !dataArr.showDesc
    apiData.forEach(item =>{
        if(item.id === dataID){
            if(dataArr.showDesc){
                [dataArr].forEach(item => renderDesc(item.description, item.id ))
            }else{
            currentCreator.innerHTML = ''
            renderDesc('', dataID)
            
        }
        }
    })
   }
   console.log(apiData)
</script>


<template>
    <div class="mother-div">
        <header>
            <div class="logo-div">
                <img class='marvel-logo' src="../images/MarvelLogo.png">
            </div>
            <div class="favorite-div">
                <h1 id="Favorite-dom">Favorites <svg height="20px" width="20px" version="1.1" id="Capa_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" viewBox="0 0 53.867 53.867" xml:space="preserve" fill="#000000"><g id="SVGRepo_bgCarrier" stroke-width="0"></g><g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g><g id="SVGRepo_iconCarrier"> <polygon style="fill:#EFCE4A;" points="26.934,1.318 35.256,18.182 53.867,20.887 40.4,34.013 43.579,52.549 26.934,43.798 10.288,52.549 13.467,34.013 0,20.887 18.611,18.182 "></polygon> </g></svg> {{ favNumber }}</h1>
            </div>
            
        </header>
        <div class="container">
            <div class="comic-container" v-for="data in apiData" :key="data.id" >
                    <div>
                        
                        <RouterLink  :to="'Comic/'+data.id"><img class="image" v-bind:src="data.thumbnail.path + '/portrait_xlarge.jpg'" /></RouterLink>
                        <h1 class="title"> {{ data.title }}</h1>
                        <button class="description-button" v-if="data.description" v-on:click="sendDesc(data.id, data)">Show Description</button>
                        <button class="creator-button" v-if="data.creators.items.length > 0" v-on:click="sendCreators(data.id, data)">CREATORS</button>
                        <button class="favorite-button" v-on:click="sendItem(data.id , data)"  v-if="!data.isFavorite">Add To Favorite</button>
                        <button class="favorite-button" v-on:click="sendItem(data.id , data)" v-if="data.isFavorite">Remove From Favorite</button>
                        <p class="description" v-if="data.showDesc"> {{ data.description }}</p>
                        <div v-bind:id="data.id" class="creators-div" v-for="(names, index) in data.creators.items" :key="'ing'+ {index}">
                            <h2 class="creators" v-if="data.showCreators"><span>Name: </span> {{ names.name }}   <span>Role: </span> {{ names.role }}</h2>
                        </div>
                    </div>
                        
                        
            </div>
        </div>
    </div>
</template>



<style lang="scss" scoped>
    
    
    header{
        
        height: 300px;
        width: 100%;
        background: #181823;
        display: flex;
        align-items: flex-end;
        
    }
    .logo-div{
        
        text-align: center;
        
    }

    .creator-button, .favorite-button, .description-button{
        
         outline: 0;
         grid-gap: 8px;
         align-items: center;
         background-color: brown;
         color: white;
         border: 1px solid #000;
         border-radius: 4px;
         cursor: pointer;
         display: inline-flex;
         flex-shrink: 0;
         font-size: 16px;
         gap: 8px;
         justify-content: center;
         line-height: 1.5;
         overflow: hidden;
         padding: 6px 16px;
         text-decoration: none;
         text-overflow: ellipsis;
         transition: all .14s ease-out;
         white-space: nowrap;
         margin: 10px 3px;
                    
                
}

    .creator-button:hover,.favorite-button:hover, .description-button:hover {
        
        box-shadow: 4px 4px 0 #000;
        transform: translate(-1px,-1px);
                    
                    
    }
    
    .creator-button:focus-visible, .favorite-button:focus-visible, .description-button:focus-visible{
        outline-offset: 1px;
    }
    .marvel-logo{
        width: 600px;
        opacity: 0.8;
        margin: 20px 20px;
        
    }
    
    
    .favorite-div{
        margin-left: auto;
    }

    #Favorite-dom{
        margin: 20px 20px;
        color: whitesmoke;
        text-shadow: 1px 1px 2px purple ;
    }
    
    .title{
        font-size: 20px;
        margin: 10px 0px;
        text-shadow: 0px 0px 3px white;
        color: rgba(0, 0, 0, 0.9);
    }
    .comic-container{
        text-align: center;
        flex-grow: 1;
        width: 17%;
        margin: 40px 20px;
        color: white;
        padding: 20px 40px;
        color: black;
        
        
    }

    .comic-container > p{
        margin-top: 20px;
        text-align: start;
        
    }

    .container{
        display: flex;
        width: 100%;
        flex-wrap: wrap;
        background: url(../images/MarvelBackgroundMain.jpg);
        background-repeat: repeat;

        margin: 0;
    }
    .description{
        font-size: 14px;
        margin: 20px 0px;
        background-color: #181823;
        color:white;
        padding:20px;
        border-radius: 20px;
    }
    
    

    span{
        color: grey;
        margin-right: 2px;
    }

    .creators-div{
        padding: 0 10px;
        margin: 0 auto;
        
        
    }
    
    .creators{
        font-size: 9.2px;
        margin: 3px auto;
        padding: 0;
        background-color: #181823;
        color:white;
        padding:20px;
        border-radius: 20px;
    }

    .image{
        transition: transform .5s ease;
        border-radius: 10px;
        box-shadow: 5px 10px 5px black;
    }

    .image:hover{
        transform: scale(1.1);
    }
    .image:active{
        opacity: 0.5;
    }
</style>