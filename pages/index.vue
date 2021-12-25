<template>
  <v-row justify="center" align="center">
    <v-col cols="12">
      <v-card color="basil">
        <v-card-title class="text-center justify-center py-6">
          <h2 class="font-weight-bold">Apriori Algorihm Result</h2>
        </v-card-title>

        <v-tabs v-model="tab" background-color="transparent" color="basil" grow>
          <v-tab v-for="item in items" :key="item">
            {{ item }}
          </v-tab>
        </v-tabs>

        <v-tabs-items v-model="tab">
          <v-tab-item :key="0">
            <v-card flat>
              <v-card-text
                >Output File: {{ resultSample.outputFile }}</v-card-text
              >
              <v-card-text>Min Support: {{ resultSample.minSupp }}</v-card-text>
              <v-card-text
                >Min Confidence: {{ resultSample.minConf }}</v-card-text
              >
              <v-card-text>Rules</v-card-text>
              <v-data-table :headers="headers" :items="resultSample.items">
              </v-data-table>
            </v-card>
          </v-tab-item>
          <v-tab-item :key="1">
            <v-card flat>
              <v-card-text
                >Output File: {{ result1000.outputFile }}</v-card-text
              >
              <v-card-text>Min Support: {{ result1000.minSupp }}</v-card-text>
              <v-card-text
                >Min Confidence: {{ result1000.minConf }}</v-card-text
              >
              <v-card-text>Rules</v-card-text>
              <v-data-table :headers="headers" :items="result1000.items">
              </v-data-table>
            </v-card>
          </v-tab-item>
          <v-tab-item :key="2">
            <v-card flat>
              <v-card-text
                >Output File: {{ result2000.outputFile }}</v-card-text
              >
              <v-card-text>Min Support: {{ result2000.minSupp }}</v-card-text>
              <v-card-text
                >Min Confidence: {{ result2000.minConf }}</v-card-text
              >
              <v-card-text>Rules</v-card-text>
              <v-data-table :headers="headers" :items="result2000.items">
              </v-data-table>
            </v-card>
          </v-tab-item>
          <v-tab-item :key="3">
            <v-card flat>
              <v-card-text
                >Output File: {{ result5000.outputFile }}</v-card-text
              >
              <v-card-text>Min Support: {{ result5000.minSupp }}</v-card-text>
              <v-card-text
                >Min Confidence: {{ result5000.minConf }}</v-card-text
              >
              <v-card-text>Rules</v-card-text>
              <v-data-table :headers="headers" :items="result5000.items">
              </v-data-table>
            </v-card>
          </v-tab-item>
          <v-tab-item :key="4">
            <v-card flat>
              <v-card-text
                >Output File: {{ resultDemo.outputFile }}</v-card-text
              >
              <!-- <v-card-text> <b>Min Support:</b>  {{resultDemo.minSupp}}</v-card-text>
              <v-card-text>Min Confidence: {{resultDemo.minConf}}</v-card-text> -->
              <v-row cols="12">
                <v-col cols="1"></v-col>
                <v-col cols="4">
                  <v-text-field
                    label="Min Support"
                    :rules="rules"
                    hide-details="auto"
                    v-model="minSupp"
                  ></v-text-field>
                </v-col>
                <v-col cols="4">
                  <v-text-field
                    label="Min Confidence"
                    :rules="rules"
                    hide-details="auto"
                    v-model="minConf"
                  ></v-text-field>
                </v-col>
                <v-col cols="2">
                  <v-btn class="mt-3" @click="genRules()"> Submit </v-btn>
                </v-col>

                <v-col cols="1"></v-col>
              </v-row>
              <v-card-text>Rules</v-card-text>
              <v-data-table :headers="headers" :items="resultDemo.items">
              </v-data-table>
            </v-card>
          </v-tab-item>
        </v-tabs-items>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: "IndexPage",
  data() {
    return {
      tab: 4,
      items: [
        "Sample Data",
        "1000 Transactions Data",
        "2000 Transactions Data",
        "5000 Transactions Data",
        "Demo Data",
      ],
      result1000: {},
      result2000: {},
      result5000: {},
      resultSample: {},
      resultDemo: {},
      rules: [(value) => !!value || "Required."],
      minSupp: localStorage.getItem("minSupp"),
      minConf: localStorage.getItem("minConf"),
    };
  },
  computed: {
    headers() {
      return [
        { text: "Left Itemset", value: "left" },
        { text: "Right Itemset", value: "right" },
        { text: "Support", value: "support" },
        { text: "Confidence", value: "confidence" },
      ];
    },
  },
  created() {
    this.$axios.get("/get-rules-1000").then((res) => {
      this.result1000 = res.data;
    });
    this.$axios.get("/get-rules-2000").then((res) => {
      this.result2000 = res.data;
    });
    this.$axios.get("/get-rules-5000").then((res) => {
      this.result5000 = res.data;
    });
    this.$axios.get("/get-rules-sample").then((res) => {
      this.resultSample = res.data;
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
  methods: {
    genRules(){
      this.$axios.post("/get-rules-demo", {
        "minSupp": this.minSupp,
        "minConf": this.minConf
      }).then((res) => {
        this.resultDemo = res.data;
        console.log(res);
        localStorage.setItem('minSupp', this.minSupp);
        localStorage.setItem('minConf', this.minConf);
    });
    },
  },
};
</script>
