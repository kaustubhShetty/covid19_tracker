<template>
  <div>
    <div class="container">
      <table class="table table-striped" style="cursor: pointer;">
        <thead class="thead-dark">
          <tr>
            <th scope="col">State/UT</th>
            <th scope="col">Confirmed</th>
            <th scope="col">Recovered</th>
          </tr>
        </thead>
        <tbody>
          <tr v-bind:key="ele.id" v-for="ele in newJsonListOfDictionaries">
            <td v-on:click="getStateName(ele.state)">{{ ele.state }}</td>
            <td>{{ ele.confirmed }}</td>
            <td>{{ ele.recovered }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>


<script>
import axios from "axios";
import { bus } from '../main';

export default {
  name: "Table",
  data() {
    return {
      originalJsonData: [],
      stateName:'mh',
      states: [
        "an",
        "ap",
        "ar",
        "as",
        "br",
        "ch",
        "ct",
        "dd",
        "dl",
        "dn",
        "ga",
        "gj",
        "hp",
        "hr",
        "jh",
        "jk",
        "ka",
        "kl",
        "la",
        "ld",
        "mh",
        "ml",
        "mn",
        "mp",
        "mz",
        "nl",
        "or",
        "pb",
        "py",
        "rj",
        "sk",
        "tg",
        "tn",
        "tr",
        "tt",
        "un",
        "up",
        "ut",
        "wb",
      ],
      totalConfirmedList: [],
      totalRecoveredList: [],
      states_daily_list: [],
      tempNumber: 0,
      temp1: {},
      dict: "",
      sum: 0,
      newJsonListOfDictionaries: [],
      //st:"",
    };
  },
  mounted() {
    axios
      .get("https://api.covid19india.org/states_daily.json")
      .then((response) => {
        this.originalJsonData = response.data;
        //this.getStates();
        this.getTotalConfirmed();
        this.getTotalRecovered();
        this.createNewJsonListOfDictionaries();
        // handle success
      })
      .catch((error) => {
        // handle error
        console.log(error);
      });
  },
  methods: {
    getTotalConfirmed() {
      this.states_daily_list = this.originalJsonData["states_daily"];
      for (let st of this.states) {
        //iterate through states list
        this.sum = 0;
        for (let i = 0; i < this.states_daily_list.length; i = i + 3) {
          //iterate through states_daily_list
          this.tempNumber = parseInt(this.states_daily_list[i][st]);
          this.sum = this.sum + this.tempNumber;
        }
        this.totalConfirmedList.push(this.sum);
        //console.log(this.totalConfirmedList);
      }
      //console.log(this.totalConfirmedList);
    },
    getTotalRecovered() {
      this.states_daily_list = this.originalJsonData["states_daily"];
      for (let st of this.states) {
        //iterate through states list
        this.sum = 0;
        for (let i = 1; i < this.states_daily_list.length; i = i + 3) {
          //iterate through states_daily_list
          this.tempNumber = parseInt(this.states_daily_list[i][st]);
          this.sum = this.sum + this.tempNumber;
        }
        this.totalRecoveredList.push(this.sum);
      }
      //console.log(this.totalRecoveredList);
    },
    createNewJsonListOfDictionaries() {
      for (let j = 0; j < this.states.length; j = j + 1) {
        this.newJsonListOfDictionaries.push({
          state: this.states[j],
          confirmed: this.totalConfirmedList[j],
          recovered: this.totalRecoveredList[j],
        });
      }
    },
    getStateName(stateName){
      this.stateName = stateName;
      this.$store.state.stateName = this.stateName;
      bus.$emit('changeIt',this.stateName);  //Helps create a channel between the table and chart component
    }
  },
};
</script>


<style>
.sortable tr {
    cursor: pointer;
}
</style>

  