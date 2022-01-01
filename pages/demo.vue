<template>
  <v-row>
    <v-col cols="1"> </v-col>
    <v-col cols="4">
      Products
      <hr />
      <br />
      <v-row v-for="item in products" :key="item">
        <v-checkbox
          class="ml-5"
          v-model="selectedProducts"
          :label="item"
          :value="item"
        ></v-checkbox>
      </v-row>
    </v-col>
    <v-col cols="6">
      Cart
      <hr />
      <v-list-item v-for="item in selectedProducts" :key="item">
        <v-list-item-content>
          <v-list-item-title v-text="item"></v-list-item-title>
        </v-list-item-content>
      </v-list-item>
      <!-- {{selectedProducts}} -->
      <div v-if="recommendedProducts.length" class="mt-3">
      <hr />
        Recommended Products
        <v-list-item v-for="item in recommendedProducts" :key="item">
          <v-list-item-content>
            <v-list-item-title v-text="item"></v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </div>
      <div v-if="recommendedProducts.length" class="mt-3">
      <hr />
        Used Rules
        <v-list-item v-for="item in usedRules" :key="item.index">
          <v-list-item-content>
            <v-list-item-title
              v-text="
                `[${item.left}] => [${
                  item.right
                }] | Confidence = ${item.confidence.toFixed(
                  2
                )} - Support = ${item.support.toFixed(2)}`
              "
            ></v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </div>
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
      usedRules: [],
    };
  },
  watch: {
    selectedProducts() {
      this.getRecommendProducts(this.selectedProducts, this.resultDemo);
    },
  },
  created() {
    this.$axios.get("/get-demo-products").then((res) => {
      this.products = res.data.products;
    });
    this.$axios
      .post("/get-rules-demo", {
        minSupp: this.minSupp,
        minConf: this.minConf,
      })
      .then((res) => {
        this.resultDemo = res.data;
        console.log(this.resultDemo);
      });
  },
  methods: {
    getRecommendProducts(selectedProducts, resultDemo) {
      this.recommendedProducts = [];
      this.usedRules = [];
      let subarray = [];
      subarray = this.getSubArrays(selectedProducts);
      for (let index = 0; index < subarray.length; index++) {
        for (let i = 0; i < resultDemo.items.length; i++) {
          if (
            JSON.stringify(subarray[index].sort()) ===
            JSON.stringify(resultDemo.items[i].left.sort())
          ) {
            this.recommendedProducts = this.recommendedProducts.concat(
              resultDemo.items[i].right
            );
            this.usedRules.push(resultDemo.items[i]);
          }
        }
      }
      this.recommendedProducts = [...new Set(this.recommendedProducts)];
    },
    getSubArrays(arr) {
      let subs = Array(Math.pow(2, arr.length)).fill();
      let result = subs
        .map((_, i) => {
          var j = -1,
            k = i,
            res = [];
          while (++j < arr.length) {
            k & 1 && res.push(arr[j]);
            k = k >> 1;
          }
          return res;
        })
        .slice(1);
      return result;
    },
  },
};
</script>
