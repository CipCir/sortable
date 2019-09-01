<template>
  <div id="app">
    <div>Please rank the below statements in your order of preference:</div>
  <br>
  <div class="answerContainer">

    <div class="answerRow" v-for="(answer,indx) in answers" :key="indx">
      <span @click="moveAnsw(false,indx,answer)" class="srtBtn left">	&#8593;</span>
      <span class="answer">{{answer}}</span>
      <span @click="moveAnsw(true,indx,answer)" class="srtBtn right">&#8595;	</span>
    </div>
  </div>

  </div>
</template>

<script>


export default {
  name: 'app',
 data(){
   return{
     answers:["First answer","Second answer","Third answer","Forth answer"]
   }
 },
 methods:{
   moveAnsw(UP,indx,currVal){     
     let origVal=""
     let newIndx
     if (UP){
       newIndx=indx+1
       if (newIndx<this.answers.length){
         origVal=this.answers[newIndx]
         this.$set(this.answers,newIndx,currVal)
         this.$set(this.answers,indx,origVal)
         
       }else{
         return false
       }

     }else{
         newIndx=indx-1
       if (newIndx>=0){
         origVal=this.answers[newIndx]
         this.$set(this.answers,newIndx,currVal)
         this.$set(this.answers,indx,origVal)
         
       }else{
         return false
       }

     }
   }
 }
}
</script>

<style>
.answerRow{
  margin:5px 0
}
.answerContainer{
      width: 50%;
    margin: 0 auto;
    text-align: left;
}
@media only screen and (max-width: 600px) {
  .answerContainer {
    width: 100%;
  }
}
.left{
  float:left;
}
.right{
  float:right
}
.srtBtn{
  cursor: pointer;
  background: lightgrey;
  margin:0 10px;
      width: 30px;
    text-align: center;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

</style>
