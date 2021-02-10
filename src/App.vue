<template id='background'>
  <div>
    <Navbar />
    <div class="modal-body row">
      <div class="col-md-6">
        <!--  first column here -->
        <Table/>
      </div>
      <div class="col-md-6">
        <!--  second column here -->
        <Chart/>
        <Form/>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from "./components/Navbar.vue";
import Table from "./components/Table.vue";
import Chart from "./components/Chart.vue";
import Form from "./components/Form.vue";



export default {
  name: "App",
  components: {
    Navbar,
    Table,
    Chart,
    Form,
  },
};

</script>


<style>
body{ 
  background-color: #ffffff;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
