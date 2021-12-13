<template>
<div class="d">
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
</form>
<div class="total">
<h2>Total d'appels</h2>
<p>{{ this.total }}</p>
<h2>Période</h2>
<p>{{dates.start}} - {{dates.end}}</p>
</div>


<div class="bar">
        <canvas id="barChart"></canvas>
    </div>
    <div>
      <canvas id="lineChart"></canvas>
    </div>
</div>
</template>

<script>
import axios from 'axios'
import { Chart } from "chart.js";
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
          total:0,
          datess:[],
          totalDay:[],
          path: [],
          globalPath: ['/','/api/batimentfromadresse','/api/batiments/adresse', '/api/batiments/infos', '/api/infos', '/api/tracking', '/api/parcelle/adresse', '/api/adresse/infos']
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
          console.log(this.jours)
        })
        .catch(error => console.log(error))
        return this.jours;
  },
  methods:{
      bargraph() {
      const ctx = document.getElementById('barChart');
      const myBarChart = new Chart(ctx, {
        type: "bar",
        data: {
          labels: this.datess,
          datasets: [{
            label: "Nombre d'appels par jour",
            data: this.totalDay,
            backgroundColor: 
              'rgb(0,0,139)',
            borderWidth: 1
          }]
        },
        options: {
              scales: {
                  xAxes: [{
                      gridLines: {
                          display:false
                      }
                  }],
                  yAxes: [{
                      gridLines: {
                          display:false
                      }   
                  }]
              },
                  plugins: {
                    title: {
                      display: true,
                      text: 'Chart.js Line Chart - Multi Axis'
                    },
                    legend: {
                      position: 'right',
                    },
                  }

                    
        }
      })

      return myBarChart
    },
     linegraph(count) {
         let tabOne= [];
         let tabTwo = [];
         let tabThree = [];
         let tabFour = [];
         let tabFifth = [];
         let tabSix = [];
         let tabSeven = [];
         let tabEight = [];
         for (let z = 0; z<count.length;z++){
             tabOne.push(count[z][0]);
             tabTwo.push(count[z][1]);
             tabThree.push(count[z][2]);
             tabFour.push(count[z][3]);
             tabFifth.push(count[z][4]);
             tabSix.push(count[z][5]);
             tabSeven.push(count[z][6]);
             tabEight.push(count[z][7]);
         }
      const ctxline = document.getElementById('lineChart');
      const lineChart = new Chart(ctxline, {
            type: 'line',
            data: {
              
                labels: this.datess,
                datasets: [
                    {
                        label: '/',
                        data: tabOne,
                        borderColor: 'rgb(0,139,139)',
                        fill: false,
                    },
                    {
                        label: '/api/batimentfromadresse',
                        data: tabTwo,
                        borderColor: 'rgb(255,0,0)',
                        fill: false,
                        
                    },
                    {
                        label: '/api/batiments/adresse',
                        data: tabThree,
                        borderColor: 'rgb(148,0,211)',
                        fill: false,
                    },
                    {
                        label: '/api/batiments/infos',
                        data: tabFour,
                        borderColor: 'rgb(0,128,128)',
                        fill: false,
                        
                    },
                    {
                        label: '/api/infos',
                        data: tabFifth,
                        borderColor: 'rgb(0,0,139)',
                        fill: false,
                       
                    },
                    {
                        label: '/api/tracking',
                        data: tabSix,
                        borderColor: 'rgb(255,140,0)',
                        fill: false,
                        
                    },
                    {
                        label: '/api/parcelle/adresse',
                        data: tabSeven,
                        borderColor: 'rgb(0,255,255)',
                        fill: false,
                       
                    },
                    {
                        label: '/api/adresse/infos',
                        data: tabEight,
                        borderColor: 'rgb(178,34,34)',
                        fill: false,
                        
                    },
                ]

            },
            options: {
                  responsive: true,
                  plugins: {
                    legend: {
                      position: 'right',
                    },
                  },
                  scales: {
                      xAxes: [{
                          gridLines: {
                              display:false
                          }
                      }]
                  }
            }
            })

        return lineChart
    },
      getValue(){
        this.total = 0;
        this.datess = [];
        this.totalDay = [];
        this.path =[];
        for(const element of this.jours){
            if(element.date>=this.dates.start & element.date<=this.dates.end){
                this.total = this.total + element.count;
                this.datess.push(element.date);
                this.totalDay.push(element.count);
                this.path.push(element.path);
            }

        }
        let t = [];
        let history = [[]];
        for (let u = 0; u<this.path.length; u++){
            t = [this.datess[u], this.totalDay[u],this.path[u]];
            history.push(t);
        }
        history.splice(0,1);
        let j = 0;
        for(let i = 0;i<this.datess.length;i++ ){
            if(this.datess[i] == this.datess[i+1]){
              let f = i;
              let c = 0;
              while(this.datess[f] == this.datess[f+1]){
                f = f+1;
                c = c+1;
              }
              for(let g = 0;g<c;g++){
                this.datess.splice(i,1);
                j = this.totalDay[i+1];
                this.totalDay[i] = this.totalDay[i]+j;
                this.totalDay.splice(i+1,1);
              }

            }
        }
        let count = [];
        for (let y = 0; y<this.datess.length;y++){
            count.push([0,0,0,0,0,0,0,0]);
        }
        t = [];
        j =-1;
        for(const el of this.datess){
            j= j+1;
            for (let e =0; e<history.length;e++){
                if (history[e][0] == el){
                    if ( history[e][2] == '/'){
                        count[j][0] = history[e][1];
                    }
                    if ( history[e][2] == '/api/batimentfromadresse'){
                        count[j][1] = history[e][1];
                    }
                    if ( history[e][2] == '/api/batiments/adresse'){
                        count[j][2] = history[e][1];
                    }
                    if ( history[e][2] == '/api/batiments/infos'){
                        count[j][3] = history[e][1];
                    }
                    if ( history[e][2] == '/api/infos'){
                        count[j][4] = history[e][1];
                    }
                    if ( history[e][2] == '/api/tracking'){
                        count[j][5] = history[e][1];
                    }
                    if ( history[e][2] == '/api/parcelle/adresse'){
                        count[j][6] = history[e][1];
                    }
                    if ( history[e][2] == '/api/adresse/infos'){
                        count[j][7] = history[e][1];
                    }
                }
            }
        }
        this.linegraph(count);
        this.bargraph();
        return this.total;

  }
      }
  }
</script>


<style scoped>
.total{
    border:solid;
    border-radius: 10px;
    background-color: rgb(42, 42, 108);
    margin-left: 35%;
    margin-right: 35%;
    margin-top: 15px;
    margin-bottom: 20px;
    
}
h2{
    color: rgb(43, 127, 201);
}
p{
    color:white;
}
button{
  background-color: rgb(42, 42, 108); 
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
  border-radius: 10px;
}

input {
  padding: 2px 5px;
  margin: 8px 0;
  display: inline-block;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}
.d{
  margin-left: 15%;
  margin-right: 15%;

}
.bar{
  margin-top: 5%;
  margin-bottom: 5%;
}
canvas{
  box-shadow: 1px 1px 12px #555;
  width: 5px;
  border-radius: 10px;
}
</style>
