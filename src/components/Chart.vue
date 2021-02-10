<template>
  <div>
    <h1>State/UT:{{ this.stateNewName }}</h1>
    <line-chart :key="componentKey" v-if="loaded" :chartdata="chartdata" /> <!--creates a line chart-->
  </div>
</template>


<script>
import axios from "axios";
import LineChart from "./LineChart.vue";
import { bus } from "../main";
// import { Line } from 'vue-chartjs';

export default {
  components: { LineChart },
  created() {
    bus.$on("changeIt", (data) => { //Helps create a channel between the table and chart component
      this.stateNewName = data;
      console.log(this.stateNewName);
      this.getConfirmedCasesforSpecificState();
      this.getRecoveredCasesforSpecificState();
      console.log(this.confirmed_cases_list);
      console.log(this.recovered_cases_list);
      this.chartdata = {
        type: "line",
        labels: this.dates,
        datasets: [
          {
            label: "Confirmed Cases",
            backgroundColor: "#00FF00",
            data: this.confirmed_cases_list,
          },
          {
            label: "Recovered Cases",
            backgroundColor: "#0000FF",
            data: this.recovered_cases_list,
          },
        ],
      };
      this.forceRerender();
      },
//Helps create a channel between the form and chart component
      bus.$on('UpdateNewDates',(data)=>{  //Updates Chart for New Range of Dates
        this.fromDate=data[0];
        this.toDate = data[1];
        console.log(this.fromDate);
        console.log(this.toDate);
        console.log(this.stateNewName);
        this.filteredResults();
        this.chartdata = {
        type: "line",
        labels: this.filteredDatesList,
        datasets: [
          {
            label: "Confirmed Cases",
            backgroundColor: "#00FF00",
            data: this.filteredConfirmedCasesList,
          },
          {
            label: "Recovered Cases",
            backgroundColor: "#0000FF",
            data: this.filteredRecoveredCasesList,
          },
        ],
      };
      this.forceRerender();
      },)  
    );
  },
  name: "Chart",
  data: () => ({
    loaded: false,
    componentKey: 0,
    dates:[], // x axis array
    filteredDatesList: [],
    filteredConfirmedCasesList: [],
    filteredRecoveredCasesList: [],
    t: "",
    stateNewName: "mh",
    states_daily_list: [],
    confirmed_cases_list: [], //confirmed array
    recovered_cases_list: [], //recovered array
    valuesList: [],
    temp: [],
    chartData: null,
    fromDate:"",
    toDate:"",
    fromDateIndex:0,
    toDateIndex:0,
  }),
  mounted() {
    //async
    this.loaded = false;
    try {
      axios
        .get("https://api.covid19india.org/states_daily.json")
        .then((response) => {
          this.originalJsonData = response.data;
          this.getDateYMD();
          this.getConfirmedCasesforSpecificState();
          this.getRecoveredCasesforSpecificState();
          this.chartdata = {
            type: "line",
            labels: this.dates,
            datasets: [
              {
                label: "Confirmed Cases",
                backgroundColor: "#00FF00",
                data: this.confirmed_cases_list,
              },
              {
                label: "Recovered Cases",
                backgroundColor: "#0000FF",
                data: this.recovered_cases_list,
              },
            ],
          };
          this.loaded = true; // handle success
        })
        .catch((error) => {
          // handle error
          console.log(error);
        });
    } catch (e) {
      console.log(e);
    }
  },
  methods: {
    forceRerender() {  //Re-renders the chart component
      this.componentKey += 1;
    },

    filteredResults(){
      for(let i=0;i<this.dates.length;i++){ //lets me get the index of from date
        if(this.dates[i]===this.fromDate){
          this.fromDateIndex=i;
          console.log(this.fromDateIndex);
        }
      }
      for(let i=0;i<this.dates.length;i++){ //lets me get the index of to date
        if(this.dates[i]===this.toDate){
          this.toDateIndex=i;
          console.log(this.toDateIndex);
        }
      }
      this.filteredDatesList= []; //Re-initializing to make the lists empty again
      this.filteredConfirmedCasesList= [];
      this.filteredRecoveredCasesList= [];
      for(let i= this.fromDateIndex;i<=this.toDateIndex;i++){ //Add filtered dates to filtered lists
        this.filteredDatesList.push(this.dates[i]); 
        this.filteredConfirmedCasesList.push(this.confirmed_cases_list[i]);
        this.filteredRecoveredCasesList.push(this.recovered_cases_list[i]);
      }
      console.log(this.filteredDatesList);
    },

    getDateYMD(){
      this.states_daily_list = this.originalJsonData["states_daily"];
      for (let i = 0; i < this.states_daily_list.length; i = i + 3) {
        this.dates.push(this.states_daily_list[i]["dateymd"]);
      }
      console.log(this.dates);
    },
    getConfirmedCasesforSpecificState() {
      this.confirmed_cases_list = [];
      console.log("ConfirmedCasesFunctionRan");
      for (let i = 0; i < this.states_daily_list.length; i = i + 3) {
        this.confirmed_cases_list.push(
          parseInt(this.states_daily_list[i][this.stateNewName])
        );
      }
      
    },
    getRecoveredCasesforSpecificState() {
      this.recovered_cases_list = [];
      console.log("RecoveredCasesFunctionRan");
      for (let i = 1; i < this.states_daily_list.length; i = i + 3) {
        this.recovered_cases_list.push(
          parseInt(this.states_daily_list[i][this.stateNewName])
        );
      }
    },
    
  },
};
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>