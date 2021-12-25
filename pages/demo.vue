<template>
  <v-row>
    <v-col cols="1"> </v-col>
    <v-col cols="5">
      Products
      <hr>
      <v-row v-for="item in products" :key="item">
        <v-checkbox v-model="selectedProducts" :label="item" :value="item"></v-checkbox>
      </v-row>
    </v-col>
    <v-col cols="5"> 
      Cart
      <hr>
      {{selectedProducts}}
    </v-col>
    <v-col cols="1"> </v-col>
  </v-row>
</template>

<script>
export default {
  name: "DemoPage",
  data() {
    return {
      minSupp: localStorage.getItem("minSupp"),
      minConf: localStorage.getItem("minConf"),
      products: [],
      selectedProducts: [],
      recommendedProducts: [], //usually purchased with
      resultDemo: {},
    };
  },
  created() {
    console.log(this.minConf, this.minSupp);
    this.$axios.get("/get-demo-products").then((res) => {
      this.products = res.data.products;
      console.log(this.products);
    });
    this.$axios
      .post("/get-rules-demo", {
        minSupp: this.minSupp,
        minConf: this.minConf,
      })
      .then((res) => {
        this.resultDemo = res.data;
      });
  },
};
</script>
