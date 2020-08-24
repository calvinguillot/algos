<template>
  <v-container fluid class="pa-0" style="background:white; height: 100vh;">
    <v-layout row wrap align-center class="pt-5 mt-0">
      <v-flex xs4 md2 offset-xs2 offset-md4 class="px-2">
        <v-select
          :items="algos"
          v-model="selAlgo"
          item-value="id"
          item-text="name"
          label="Algorithm"
        ></v-select>
      </v-flex>
      <v-flex xs4 md2 class="px-2">
        <v-select :items="numArr" label="Elements" v-model="selNum"></v-select>
      </v-flex>
      <v-flex xs12 sm2 md1 text-xs-center>
        <v-btn small fab :disabled="btnDisabled" class="my-0" color="primary" @click="algorithms()">
          <v-icon>mdi-shuffle</v-icon>
        </v-btn>
      </v-flex>
      <v-flex xs12 text-xs-center>
        <line-chart :chart-data="chartArr" :options="options" :height="500" class="pa-3"></line-chart>
      </v-flex>
      <v-flex style="position:absolute; bottom:20px; right:10px;z-index:3">
        <v-btn fab small class="black white--text" @click="dialog= true" style="font-size:150%;">?</v-btn>
      </v-flex>
    </v-layout>
    <v-dialog v-model="dialog" width="500">
      <v-card>
        <v-card-title class="headline" primary-title>Sorting Algorithms Viz</v-card-title>
        <v-divider></v-divider>
        <v-card-text>
          The X axis represents the number of iterations, while the Y axis represents the position of the elements of the set. These visualisations thus, depicts the evolution of the set throughout time, rather than showcasing one state at a time.
          <br />
          <br />Feel free to play around with the values and see what you get. This project was inspired by the work by Aldo Cortesi
          <a
            href="https://corte.si/posts/code/visualisingsorting/index.html"
          >here</a> and Francisco Couzo
          <a href="https://franciscouzo.github.io/sort/">here</a>.
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
import LineChart from "./LineChart";
import Rainbow from "rainbowvis.js";
export default {
  name: "Algos",
  data() {
    return {
      dialog: false,
      btnDisabled: false,
      selNum: 10,
      selAlgo: 1,
      numArr: [5, 10, 20, 50],
      algos: [
        { id: 1, name: "Insertion" },
        { id: 2, name: "Bubble" },
        { id: 3, name: "Cocktail" },
        { id: 4, name: "Radix" },
      ],
      iterations: [],
      chartArr: null,
      sortedArr: [],
      options: {
        tooltips: { enabled: false },
        hover: { mode: null },
        animation: {
          duration: 0,
        },
        elements: {
          line: {
            tension: 0.1,
          },
        },
        layout: {
          padding: {
            left: 10,
            right: 0,
            top: 50,
          },
        },
        legend: {
          labels: {
            fontColor: "white",
          },
          position: "bottom",
          display: false,
        },
        scales: {
          yAxes: [
            {
              ticks: {
                beginAtZero: true,
                fontColor: "black",
                min: 0,
                padding: 0,
                display: false,
              },
              gridLines: {
                color: "grey",
                display: false,
                drawBorder: false,
              },
              display: true,
              position: "right",
            },
          ],
          xAxes: [
            {
              ticks: {
                fontColor: "black",
                padding: 25,
                display: false,
              },
              gridLines: {
                display: true,
                drawBorder: false,
              },
              display: true,
            },
          ],
        },
        responsive: true,
        maintainAspectRatio: false,
      },
    };
  },
  components: {
    LineChart,
  },
  methods: {
    mainArr(n) {
      return new Array(n)
        .fill()
        .map((a, i) => (a = i))
        .sort(() => Math.random() - 0.5);
    },
    algorithms() {
      // Clear Data
      this.iterations = [];
      this.sortedArr = [];
      let iters = 0;
      let baseArr = this.mainArr(this.selNum);
      let xArr = [];
      // Push Base Array
      this.iterations.push(iters);
      this.iterations.push(iters);
      this.sortedArr.push(Array.from(baseArr));
      // Select Algorithm
      switch (this.selAlgo) {
        case 1:
          xArr = this.insertion(baseArr, iters);
          this.sortedArr.push(xArr[0]);
          break;
        case 2:
          this.sortedArr.push(Array.from(baseArr));
          xArr = this.bubble(baseArr, iters);
          break;
        case 3:
          this.sortedArr.push(Array.from(baseArr));
          xArr = this.cocktail(baseArr, iters);
          // this.sorterArr.pop()
          break;
        case 4:
          this.sortedArr.push(Array.from(baseArr));
          xArr = this.radix(baseArr, iters);
          break;
        default:
      }
      // Push Final Array
      this.sortedArr.push(xArr[0]);
      this.iterations.push(xArr[1]);
      // Populate Chart
      this.populator(this.sortedArr);
    },
    insertion(arr, ite) {
      for (let i = 1; i < arr.length; i++) {
        for (let j = 0; j < i; j++) {
          if (arr[i] < arr[j]) {
            // Sorted Iteration
            this.sortedArr.push(Array.from(arr));
            // Iterations
            this.iterations.push(ite++);
            const [element] = arr.splice(i, 1);
            arr.splice(j, 0, element);
          }
        }
      }
      return [arr, ite];
    },
    cocktail(arr, ite) {
      let sorted = true;
      while (sorted) {
        for (let i = 0; i < arr.length - 1; i++) {
          if (arr[i] > arr[i + 1]) {
            let temp = arr[i];
            arr[i] = arr[i + 1];
            arr[i + 1] = temp;
            sorted = true;
          }
        }
        if (!sorted) break;

        sorted = false;
        for (let j = arr.length - 1; j > 0; j--) {
          if (arr[j - 1] > arr[j]) {
            let temp = arr[j];
            arr[j] = arr[j - 1];
            arr[j - 1] = temp;
            sorted = true;
          }
        }
        this.sortedArr.push(Array.from(arr));
        this.iterations.push(ite++);
      }
      return [arr, ite];
    },
    bubble(arr, ite) {
      let swapped;
      let n = arr.length - 1;
      do {
        swapped = false;
        for (let i = 0; i < n; i++) {
          // if left element > right element, swap
          if (arr[i] > arr[i + 1]) {
            const temp = arr[i];
            arr[i] = arr[i + 1];
            arr[i + 1] = temp;
            swapped = true;
            this.sortedArr.push(Array.from(arr));
            this.iterations.push(ite++);
          }
        }
      } while (swapped);
      return [arr, ite];
    },
    radix(arr, ite) {
      const maxNum = Math.max(...arr) * 10;
      let divisor = 10;
      while (divisor < maxNum) {
        let buckets = [...Array(10)].map(() => []);

        for (let num of arr) {
          buckets[Math.floor((num % divisor) / (divisor / 10))].push(num);
        }
        arr = [].concat.apply([], buckets);
        this.sortedArr.push(Array.from(arr));
        this.iterations.push(ite++);

        divisor *= 10;
      }
      return [arr, ite];
    },
    transposer(e) {
      // Transpose Array
      let transArr = Object.keys(e[0]).map(function (c) {
        return e.map(function (r) {
          return r[c];
        });
      });
      return transArr;
    },
    populator(e) {
      let preArr = this.transposer(e);
      let j = 0;

      // Animate Array
      let counter = setInterval(() => {
        this.btnDisabled = true;
        let animArr = [];
        for (let i = 0; i < preArr.length; i++) {
          animArr.push(preArr[i].slice(0, j + 1));
        }
        this.fillChart(animArr);
        j++;
        if (j >= preArr[0].length) {
          clearInterval(counter);
          this.btnDisabled = false;
        }
      }, 40);
    },
    colourSet(n) {
      var rainbow = new Rainbow();
      rainbow.setNumberRange(1, n);
      rainbow.setSpectrum("#440154", "#228b8d", "#f8e621");
      let colourArr = [];
      for (var i = 1; i <= n; i++) {
        var hexColour = rainbow.colourAt(i);
        colourArr.push(`#${hexColour}e6`);
      }
      return colourArr.reverse();
    },
    fillChart(e) {
      let dataArr = [];
      // Array of position per line
      for (let i = 0; i < e.length; i++) {
        dataArr.push({
          borderColor: this.colourSet(e.length)[i],
          borderWidth: 10,
          backgroundColor: "transparent",
          pointRadius: 0,
          data: e[i],
        });
      }
      // Fill Chart
      this.chartArr = {
        labels: this.iterations,
        datasets: dataArr,
      };
    },
  },
  mounted() {
    this.algorithms();
  },
};
</script>

<style scoped>

::-webkit-scrollbar {
  display: none;
}

.v-dialog .v-dialog--active .v-card {
  border-radius: 30px !important;
}
</style>