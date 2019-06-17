<template>
  <div id="app">
    <Canvas>
      <g v-for="(row, i) in matrix" :key="'row-'+i" :y="i">
        <Cell
          v-for="(cell, j) in row"
          :key="j"
          :x="j * cellSize"
          :y="i * cellSize"
          :size="cellSize"
          @click.native="flip(i, j, cell)"
          :fill="concentration(cell)"
        ></Cell>
      </g>
    </Canvas>
    <button @click="reactionDiffusion()">RUN ALGO</button>
  </div>
</template>

<script>
import Canvas from "./components/Canvas.vue";
import Cell from "./components/Cell.vue";

export default {
  name: "app",
  components: {
    Canvas,
    Cell
  },
  data() {
    return {
      pixelSize: 720,
      cellSize: 4,
      matrix: []
    };
  },
  methods: {
    concentration(cell) {
      //https://www.karlsims.com/rd.html

      //if (cell === 0) return "#0000ff";
      //else return "#ffff00";

      const A = cell[0];
      const B = cell[1];

      function light(val) {
        return 255 * val;
      }
      function dark(val) {
        return 255 - 255 * val;
      }
      const AVG = Math.sqrt((Math.pow(light(A), 2) + Math.pow(dark(B), 2)) / 2);
      return `rgb(${AVG}, ${AVG}, ${AVG})`;
    },
    flip(i, j, cell) {
      this.matrix[i][j].reverse();
    },
    reactionDiffusion() {
        const $this = this;
        const D_A = 1;
        const D_B = 0.5;
        const F = 0.055;
        const K = 0.062;
        const DELTA_T = 1;
        const KERNEL = [
            0.05, 0.2, 0.05,
            0.2, -1, 0.2,
            0.05, 0.2, 0.05
            ]
        
        function convolve(i, j, val, type) {
            const proximity = getProximityMatrix(i, j, type);
           if (proximity !== false){
               let E = 0;

               KERNEL.forEach(function(w, i){
                   E = E + (w * proximity[i]) 
               })
 
                console.log(E);
               return E;
           }
           return val;
        }

        function getProximityMatrix(i, j, type) {
            const TYPE = (type === 0) ? 0 : 1;
            if(i > 0 && i < $this.matrix.length - 1){
                if(j > 0 && j < $this.matrix[i].length - 1){
                    return [
                        $this.matrix[i-1][j-1][TYPE], $this.matrix[i-1][j][TYPE],$this.matrix[i-1][j+1][TYPE],
                        $this.matrix[i][j-1][TYPE], $this.matrix[i][j][TYPE],$this.matrix[i][j+1][TYPE],
                        $this.matrix[i+1][j+1][TYPE], $this.matrix[i+1][j][TYPE],$this.matrix[i+1][j+1][TYPE],
                    ]
                } else return false;
            } else return false;
        }
        this.matrix.forEach(function(row, i){

            row.forEach(function(cell, j){
                const A = cell[0];
                const B = cell[1];

                const NEW_A = A + (D_A * convolve(i,j, A, 0) - A * Math.pow(B, 2) + F * (1 - A)) * DELTA_T;
                const NEW_B = B + (D_B * convolve(i,j, B, 1) + A * Math.pow(B, 2) - (K + F) * B) * DELTA_T

                //$this.matrix[i][j] = [NEW_A, NEW_B];
                $this.matrix[i].splice(j, 1, [NEW_A, NEW_B]);
            })
        })
    }
  },
  computed: {
    size() {
      return this.pixelSize / this.cellSize;
    }
  },
  created() {
    for (let i = 0; i < this.size; i++) {
      //add rows
      const ROW = [];
      for (let j = 0; j < this.size; j++) {
        ROW.push([1, 0]);
      }
      this.matrix.push(ROW);
    }
  }
};
</script>

<style>
#app {
  font-family: Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #000;
}
</style>
