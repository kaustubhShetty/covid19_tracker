<template>
  <div>
    <br />
    <div
      class="container-fluid pt-4"
      style="border: 1px solid #cecece; border-radius: 15px"
    >
    <h3 style="text-align: center">Set Date Range</h3>
      <div class="card">
        <div class="card-body">
          <label for="from">From :</label> -->
          <input type="date" id="from" name="from" min="2020-03-14" /><br />
        </div>
      </div>
      <br />
      <div class="card">
        <div class="card-body">
          <label for="to">To :</label> -->
          <input type="date" id="to" name="to" /><br>
        </div>
      </div>
      <br />
      <button type="button" class="btn btn-primary" v-on:click="getTheValues">
        Click Me</button
      ><br /><br />
    </div>
  </div>
</template>


<script>
import { bus } from "../main";

export default {
  name: "Form",
  data() {
    return {
      fromVar: "2020-03-20", //20-Mar-20
      toVar: "2020-04-09", //09 Apr 2020
    };
  },
  methods: {
    getTheValues() {
      //console.log(typeof document.getElementById("from").value);
      //console.log(typeof document.getElementById("to").value);
      this.fromVar = document.getElementById("from").value;
      this.toVar = document.getElementById("to").value;
      bus.$emit("UpdateNewDates", [this.fromVar, this.toVar]);//Helps create a channel between the Form and Chart component
    },
  },
};
</script>


<style>
</style>