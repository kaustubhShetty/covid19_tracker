<template>
  <div>
    <h1>State/UT:{{ this.stateNewName }}</h1>
    <!-- this.$store.state.stateName -->
    <!-- v-on:change="reload" -->
    <!-- <input type="hidden" value="mh" v-on:change= "this.trigger()" />  -->
    <line-chart :key="componentKey" v-if="loaded" :chartdata="chartdata" />
    <!-- <div>{{ this.getConfirmedCasesforSpecificState() }}</div> -->
  </div>
</template>


<script>
import axios from "axios";
import LineChart from "./LineChart.vue";
import store from "../store";
import { bus } from "../main";

// import { Line } from 'vue-chartjs';

export default {
  components: { LineChart },
  created() {
    bus.$on("changeIt", (data) => {
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
      //this.$refs.chart.render()
      },

      bus.$on('UpdateNewDates',(data)=>{  //Updates Chart for New Range of Dates
        this.fromDate=data[0];
        this.toDate = data[1];
        console.log(this.fromDate);
        console.log(this.toDate);
        console.log(this.stateNewName);
        this.filteredResults();
        //call method to add to filtered lists
        //call chart function and render the chart.
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
    date: "",
    //dateymd:[],
    filteredDatesList: [],
    filteredConfirmedCasesList: [],
    filteredRecoveredCasesList: [],
    t: "",
    month: {
      Jan: "01",
      Feb: "02",
      Mar: "03",
      Apr: "04",
      May: "05",
      Jun: "06",
      Jul: "07",
      Aug: "08",
      Sept: "09",
      Oct: "10",
      Nov: "11",
      Dec: "12",
    },
    stateNewName: "mh",
    states_daily_list: [],
    stateNameDuplicate: "",
    confirmed_cases_list: [], //confirmed array
    recovered_cases_list: [], //recovered array
    valuesList: [],
    temp: [],
    half_way: [],
    DictionaryOfDatesAndConfirmedProperty: {},
    DictionaryOfDatesAndRecoveredProperty: {},
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
          //this.getStates();
          //this.getAllDates();
          this.getDateYMD();
          this.getConfirmedCasesforSpecificState();
          this.getRecoveredCasesforSpecificState();
          //this.DictionaryOfDatesAndConfirmed();
          //this.DictionaryOfDatesAndRecovered();
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
          this.loaded = true;
          // handle success
          //console.log(this.originalJsonData);
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
    reload() {
      //this.trigger();
      this.$forceUpdate();
    },

    forceRerender() {
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

    convertDate() {
      for (let i = 0; i < this.dates.length; i++) {
        this.t = this.dates[i].split("-");
        this.dates[i] = `${20 + this.t[2]}-${this.month[this.t[1]]}-${this.t[0]} 00:00:00 -0800`;
      }
      //console.log(this.dates);
    },
    getDateYMD(){
      this.states_daily_list = this.originalJsonData["states_daily"];
      for (let i = 0; i < this.states_daily_list.length; i = i + 3) {
        this.dates.push(this.states_daily_list[i]["dateymd"]);
      }
      console.log(this.dates);
    },
    // getAllDates() {
    //   this.states_daily_list = this.originalJsonData["states_daily"];
    //   //console.log(this.states_daily_list);
    //   for (let i = 0; i < this.states_daily_list.length; i = i + 3) {
    //     this.dates.push(this.states_daily_list[i]["date"]);
    //   }
    //   this.convertDate();
    //   console.log(this.dates);
    // },
    getConfirmedCasesforSpecificState() {
      this.confirmed_cases_list = [];
      console.log("ConfirmedCasesFunctionRan");
      for (let i = 0; i < this.states_daily_list.length; i = i + 3) {
        this.confirmed_cases_list.push(
          parseInt(this.states_daily_list[i][this.stateNewName])
        );
      }
      //console.log(this.confirmed_cases_list);
      //return this.confirmed_cases_list;
    },
    getRecoveredCasesforSpecificState() {
      this.recovered_cases_list = [];
      console.log("RecoveredCasesFunctionRan");
      for (let i = 1; i < this.states_daily_list.length; i = i + 3) {
        this.recovered_cases_list.push(
          parseInt(this.states_daily_list[i][this.stateNewName])
        );
      }
      // console.log(this.recovered_cases_list);
    },
    // DictionaryOfDatesAndConfirmed() {
    //   this.getConfirmedCasesforSpecificState(this.$store.state.stateName);
    //   for (let i = 0; i < this.dates.length; i++) {
    //     this.DictionaryOfDatesAndConfirmedProperty[
    //       this.dates[i]
    //     ] = this.confirmed_cases_list[i];
    //   }
    //   console.log(this.DictionaryOfDatesAndConfirmedProperty);
    //   //return this.DictionaryOfDatesAndConfirmedProperty;
    // },
    // DictionaryOfDatesAndRecovered() {
    //   this.getRecoveredCasesforSpecificState(this.$store.state.stateName);
    //   for (let i = 0; i < this.dates.length; i++) {
    //     this.DictionaryOfDatesAndRecoveredProperty[
    //       this.dates[i]
    //     ] = this.recovered_cases_list[i];
    //   }
    //   console.log(this.DictionaryOfDatesAndRecoveredProperty);
    //   //return this.DictionaryOfDatesAndRecoveredProperty;
    // },
  },

  watch: {
    "$store.state.stateName": () => {
      console.log(store.state.stateName);
      //     // console.log("Hahahahahah");
      //   // for (let i = 0; i < this.states_daily_list.length; i = i + 3) {
      //   //     this.confirmed_cases_list.push(
      //   //       parseInt(this.states_daily_list[i][statename])
      //   //     );
      //   //    }

      //     //location.reload();
      //     //this.$forceUpdate();
      //     //this.stateNameDuplicate = store.state.stateName;
      //     // //console.log(this.stateNameDuplicate);
      //     // this.getConfirmedCasesforSpecificState(store.state.stateName);
      //     // this.getRecoveredCasesforSpecificState(store.state.stateName);
      //   }
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

// {
//            "2017-01-01 00:00:00 -0800": 5,
//            "2017-01-02 00:00:00 -0800": 3,
//          },



// {
//            "2017-01-01 00:00:00 -0800": 5,
//            "2017-01-02 00:00:00 -0800": 3,
//          },

//{'2017-01-01 00:00:00 -0800': 3, '2017-01-02 00:00:00 -0800': 4}