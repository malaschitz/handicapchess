<template>
  <div id="app">
    <h1>Random Handicap Chess Generator</h1>
    <h3>Handicap poistion {{ha}}.{{po}}/{{pos}}</h3>

      <chessboard :fen="fen"></chessboard>


    <template v-for="i in 21">
      <button type="button"  href="" @click.prevent="handicap(i-1)">
        {{i-1}}
      </button>&nbsp;
    </template>

    <h4>FEN: {{fen}}</h4>

  </div>
</template>

<script>
import {chessboard} from 'vue-chessboard'
import 'vue-chessboard/dist/vue-chessboard.css'

export default {
  name: 'app',

  data:  function(){
    return {
      ha: 0,
      po: 1,
      pos: 1,
      fen: "",

      stones: ['r','n','b','q','k','b','n','r', 'p','p','p','p','p','p','p','p'],
      values: [5,3,3,9,999,3,3,5,1,1,1,1,1,1,1,1],

    }
  },

  components: {
    chessboard
  },

  methods: {
    handicap: function(h) {
      this.ha = h;
      if (h == 0) {
        this.fen = "rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1";
        this.po = 1;
        this.pos = 1;
      } else {
        this.po = -1;
        this.calcPositions();
        this.po = Math.floor(Math.random() * this.pos) + 1;
        this.calcPositions();
      }
    },

    calcPositions: function() {
      this.pos = 0;
      var chosen = [0,0,0,0,0,0,0,0, 0,0,0,0,0,0,0,0];
      this.rekur(0,0,chosen);
    },

    rekur: function(depth,sum,chosen) {
      if (depth == 16) {
        return;
      }
      for (var i=1;i>=0;i--) {
        if (i === 1) {
          if (sum+this.values[depth] > this.ha) {
            continue;
          } else if (sum + this.values[depth] === this.ha) {
            // new position
            this.pos = this.pos + 1;
            if (this.pos == this.po) {
              chosen[depth] = 1;
              this.fenCalc(chosen);
              chosen[depth] = 0;
            }
            continue;
          } else {
            chosen[depth] = 1;
            sum = sum + this.values[depth];
            this.rekur(depth+1,sum,chosen);
            chosen[depth] = 0;
            sum = sum - this.values[depth];
          }
        } else {
          this.rekur(depth+1,sum,chosen);
        }
      }
      return;
    },

    fenCalc: function(chosen) {
      var fen = "";
      for (var i=0;i<8;i++) {
        if (chosen[i] == 0) {
          fen = fen + this.stones[i];
        } else {
          fen = fen + "1";
        }
      }
      fen = fen + "/";
      for (var i=0;i<8;i++) {
        if (chosen[i+8] == 0) {
          fen = fen + "p";
        } else {
          fen = fen + "1";
        }
      }
      fen = fen + "/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1";
      fen = fen.replace(/11111111/g, "8")
      fen = fen.replace(/1111111/g, "7")
      fen = fen.replace(/111111/g, "6")
      fen = fen.replace(/11111/g, "5")
      fen = fen.replace(/1111/g, "4")
      fen = fen.replace(/111/g, "3")
      fen = fen.replace(/11/g, "2")

      this.fen = fen;
    },

  },





}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
