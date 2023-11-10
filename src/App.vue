<template>
  <div class="container">
    <div class="cats" :style="'width:' + catWidth + '%;'" @click="catClick">
      <div class="info-container">
        <div class="percent-container">
          {{ Math.round(catPercent) }}%
        </div>
        <div class="vote-container">
          {{ cats }} голосов
        </div>
      </div>
    </div>
    <div class="dogs" :style="'width:' + dogWidth + '%;'" @click="dogClick">
      <div class="info-container">
        <div class="percent-container">
          {{ Math.round(dogPercent) }}%
        </div>
        <div class="vote-container">
          {{ dogs }} голосов
        </div>
      </div>
    </div>
    
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data(){
    return {
      cats: null,
      dogs: null,
      connection: null
    }
  },
  methods:{
    async catClick(){
      const res = await fetch("https://danilka.site/api/v1/cat", {
        method: 'PUT'
      })
      console.log(res)
    },
    async dogClick(){
      const res = await fetch("https://danilka.site/api/v1/dog", {
        method: 'PUT'
      })
      console.log(res)
    }
  },
  created: function(){
    this.connection = new WebSocket("wss://danilka.site/api/w")
    this.connection.onopen = function(event){
      console.log(event)
    }
    const obj = this
    this.connection.onmessage = function(event){
      const response = JSON.parse(event.data)
      obj.cats = response.cat_count
      obj.dogs = response.dog_count
    }
  },
  computed:{
    catWidth: function(){
      var result = this.catPercent
      if (result > 90){
        result = 90
      } else if (result < 10){
        result = 10
      }
      return result;
    },
    dogWidth: function(){
      return 100 - this.catWidth;
    },
    catPercent: function(){
      if (this.cats + this.dogs == 0){
        return 50
      }
      return (this.cats / (this.cats + this.dogs)) * 100; 
    },
    dogPercent: function(){
      return 100 - this.catPercent;
    }
  }
}
</script>

<style>
*{
  margin: 0;
}

.container{
  height: 100vh;
  background-color: black;
  display: flex;
}
.cats{
  height: 100vh;
  background-color: aqua;
  background-image: url(https://99px.ru/sstorage/53/2019/05/tmb_259612_992467.jpg);
  background-size: cover, 100vh;
  background-repeat: no-repeat;
  background-position: 50%;
  transition: width, 0.5s;
  border-right: 4px solid #fff;
}
.dogs{
  height: 100vh;
  transition: width, 0.5s;
  background-image: url(https://png.pngtree.com/background/20230513/original/pngtree-shiba-inu-is-standing-in-the-leaves-picture-image_2505406.jpg);
  background-size: cover, 100vh;
  background-repeat: no-repeat;
  background-position: 50%;
  background-color: red;
  border-left: 4px solid #fff;
}
.percent-container{
  color: white;
  font-weight: 700;
  font-size: 72px;
  font-family: Arial, Helvetica, sans-serif;
  margin-top: 24px;
  width: fit-content;
}
.info-container{
  user-select: none;
  display: flex;
  justify-content: center;
  align-items: flex-end;
}
.vote-container{
  font-weight: 900;
  color: #ffffffc0;
  font-family: Arial, Helvetica, sans-serif;
  padding-bottom: 10px;
}
</style>
