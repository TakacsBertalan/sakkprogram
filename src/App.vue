
<template>
  <div id="app" class="text-center">
    <table>
      <tr v-for="(row,j) in table">
        <td
          v-for="(cell,i) in row"
          v-html="cell"
          :class="'f'+f+cell+' x'+((i+j)%2)+' '+(ac.x===i&&ac.y===j?'a'+((i+j)%2):'')"
          @click="clickOnTableCell(i,j)"
        ></td>
      </tr>
    </table>
    <p id="next">A világos kezd</p>
    <div>{{debug}}</div>
  </div>
</template>

<style>
@import url("https://fonts.googleapis.com/css?family=Calibre");
#app {
  margin: 20px;
  font-family: "Comic Sans";
  font-size: 40px;
}
td {
  margin: 2px;
  border-radius: 3px 3px;
  border: solid 1px;
  width: 80px;
  height: 80px;
  vertical-align: top;
  text-align: center;
  cursor: pointer;
  box-shadow: 1px 1px 3px;
}
td.f {
  cursor: default;
}
td.x0 {
  background-color: rgb(255, 229, 204);
}
td.x1 {
  background-color: rgb(204, 0, 0);
}
div.ed {
  font-family: "Courgette", cursive;
}
td.a0 {
  border: solid 3px;
  background-color: white;
}
td.a1 {
  border: solid 3px;
  background-color: rgb(213, 210, 245);
}
table {
  margin: 0 auto;
  border-collapse: separate;
  border-spacing: 3px;
}
p {
  color: rgb(39, 35, 35);
  text-align: center;
  size: 12 ch;
}
</style>
<script>
export default {
  name: "app",
  data() {
    return {
      ac: {},
      f: "",
      mf: "",
      next: true,
      debug: "",
      nextStep: [],
      table: [
        [" ", "&#9823;", " ", "&#9823;", " ", "&#9823;", " ", "&#9823;"],
        [" ", " ", " ", " ", " ", " ", " ", " "],
        [" ", " ", " ", " ", " ", " ", " ", " "],
        [" ", " ", " ", " ", " ", " ", " ", " "],
        [" ", " ", " ", " ", " ", " ", " ", " "],
        [" ", " ", " ", " ", " ", " ", " ", " "],
        [" ", " ", " ", " ", " ", " ", " ", " "],
        [" ", " ", " ", " ", "&#9817;", " ", " ", " "]
      ],
      black: ["&#9823;"],
      white: ["&#9817;"]
    };
  },

  methods: {
    newRound(){
      this.ac = {},
      this.f = "",
      this.mf = "",
      this.next = true,
      this.debug = "",
      this.table = [
      [" ", "&#9823;", " ", "&#9823;", " ", "&#9823;", " ", "&#9823;"],
      [" ", " ", " ", " ", " ", " ", " ", " "],
      [" ", " ", " ", " ", " ", " ", " ", " "],
      [" ", " ", " ", " ", " ", " ", " ", " "],
      [" ", " ", " ", " ", " ", " ", " ", " "],
      [" ", " ", " ", " ", " ", " ", " ", " "],
      [" ", " ", " ", " ", " ", " ", " ", " "],
      [" ", " ", " ", " ", "&#9817;", " ", " ", " "]
      ],
      document.getElementById("next").innerHTML = "A világos kezd";

    },

    whiteWinCheck(ym) {
      if (ym === 0) {
        window.alert("A világos nyert!");
        this.newRound()
      }
    },

    checkPossibleStepsDark(f, y, x) {
      this.nextStep = [];
      if (f === "&#9823;" && y !== 7) {
        for (let i = 0; i < 8; i++) {
          if ((i === x + 1 || i === x - 1) && (this.table[y + 1][i] === " ")) {
            this.nextStep.push({row : y + 1, column : i});
          }
        }
      } else if (f === "&#9817;" && y !== 0) {
          for (let i = 0; i < 8; i++) {
            if (i === x + 1 || i === x - 1) {
                if (this.table[y - 1][i] === " ") {
                  this.nextStep.push({row : y - 1, column : i});
                }
            }
          }
          for (let i = 0; i < 8; i++) {
            if (i === x + 1 || i === x - 1) {
                if (this.table[y + 1] && this.table[y + 1][i] && this.table[y + 1][i] === " ") {
                  this.nextStep.push({row : y + 1, column : i});
                }
            }
          }
          if (this.nextStep && this.nextStep.length === 0) {
            window.alert("A sötétek nyertek!");
            this.newRound();
          }
      }
    },

    whoIsNext(f) {
      let nextPlayer = "";
      if (f === "&#9823;" || this.f === "") {
        nextPlayer = "A világos következik";
      } else {
        nextPlayer = "A sötét következik";
      }
      document.getElementById("next").innerHTML = nextPlayer;
    },

    goodMovement(x, y, f, xm, ym) {
      let ret = false;
      
      if (f === "&#9823;") {
        if (this.table[ym][xm] === " ") {
          if ((x === xm + 1 || x === xm - 1) && y === ym - 1) {
            ret = true;
            this.whoIsNext(f);
          }
        }
      } else if (f === "&#9817;") {
        if (this.table[ym][xm] === " ") {
          if ((x === xm + 1 || x === xm - 1) && (y === ym + 1 || y === ym - 1)) {
            ret = true;
            this.whoIsNext(f);
          }
        }
      } else if (
        (this.next && this.black.includes(this.table[ym][xm])) ||
        (!this.next && this.white.includes(this.table[ym][xm]))
      ) ret = false;
      return ret;
    },

    clickOnTableCell(x, y) {
      if (x === this.ac.x && y === this.ac.y) {
        this.ac = {};
        this.f = "";
        this.next = !this.next;
      } else if (this.f === "x") {
        if (this.goodMovement(this.ac.x, this.ac.y, this.mf, x, y)) {
          this.table[y][x] = this.mf;
          this.table[this.ac.y][this.ac.x] = " ";
          this.ac = {};
          this.f = "";
          this.whiteWinCheck(y);
        }
      } else if (this.table[y][x] != " ") {
        if (
          (!this.next && this.black.includes(this.table[y][x])) ||
          (this.next && this.white.includes(this.table[y][x]))
        ) {
          this.next = !this.next;
          this.ac = { x, y };
          this.mf = this.table[y][x];
          this.checkPossibleStepsDark(this.mf, y, x);
          this.f = "x";
        }
      }
    }
  }
};
</script>

