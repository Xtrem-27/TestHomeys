<template>
<form>
<div>
    <label for="date-debut">Début</label>
    <input type="date" id="debut" name="debut" v-model="dates.start" :max="Max" @input="(value) => (Min = value)">
    <label for="date-fin">Fin</label>
    <input type="date" id="fin" name="fin" v-model="dates.end" :min="Min" @input="(value) => (Max = value)">
</div>
<div>
    <button type="button" v-on:click="getValue">tracking</button>
</div>

<div class="total">
<h2>Total d'appels</h2>
<p>{{ this.x }}</p>
<h2>Période</h2>
<p>{{dates.start}} - {{dates.end}}</p>
</div>

</form>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Date',
  data(){
      return{
          dates:{
              start:null,
              end: null,
          },
          Min:'',
          Max:'',
          jours:null,
          x:0,
      };
  },mounted(){
      axios
    .get('http://api-buildings.homeys.io/api/tracking', {
          contentType: "application/json; charset=utf-8;",
          headers:{
            Authorization:
                "t8dwXjjIjrRq54LSq1Vt3SmWSYi9K7jbVzP5UdgD7ptPTJE5N0"
          }
        }
    )
        .then(res => {
          this.jours = res.data
        })
        .catch(error => console.log(error))
        return this.jours;
  },  
  methods:{
      getValue(){
        this.x = 0;
        for(const element of this.jours){
            if(element.date>=this.dates.start & element.date<=this.dates.end){
                this.x = this.x + element.count;
            }
        }
        return this.x;
             
  }
      }
  }
 

  

</script>


<style scoped>
.total{
    border:solid;
    border-radius: 10px;
    background-color: rgb(42, 42, 108);
    margin-left: 30%;
    margin-right: 30%;
    
}
h2{
    color: rgb(43, 127, 201);
}
p{
    color:white;
}
</style>
