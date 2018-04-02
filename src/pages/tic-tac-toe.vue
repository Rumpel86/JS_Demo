<template>
  <main-layout>
    <p>tic-tac-toe</p>
    <board></board>
    <button-showWinnner></button-showWinnner>
    <popupMenuForButton></popupMenuForButton>
  </main-layout>
</template>

<style>
.popupMenu-mask {
  position: absolute;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: table;
  transition: 
    opacity 0.3s ease;
}

.popupMenu-wrapper {
  position: absolute;
  display: table-cell;
}

.popupMenu-container {
  width: 300px;
  margin: 0px auto;
  padding: 10px 20px;
  background-color: rgba(180, 219, 180, 1);
  border-radius: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.5);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.popupMenu-header h3 {
  margin-top: 0;
  color: #42b983;
}

.popupMenu-body {
  margin: 20px 0;
}

.popupMenu-default-button {
  float: right;
}

/*
 * The following styles are auto-applied to elements with
 * transition="popupMenu" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the popupMenu transition by editing
 * these styles.
 */

.popupMenu-enter {
  opacity: 0;
}

.popupMenu-leave-active {
  opacity: 0;
}

.popupMenu-enter .popupMenu-container,
.popupMenu-leave-active .popupMenu-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>

<style>
table {
  margin: 10px auto;
}
td {
  width: 60px;
  height: 60px;
  border: 2px solid #555555;
  text-align: center;
  vertical-align: middle;
  font-size: 28px;
  padding: 0;
}

button {
  display: block;
  margin: 0 auto;
  width: 10em;
  font-size: 20px;
}
</style>

<style>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 300px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>


<script>
import Vue from "vue";
import MainLayout from "../layouts/Main.vue";
import sw from "./showWinner.vue";
import pm from "./showMenu.vue";

export default {
  components: {
    MainLayout
  }
};

let arrTTT = [];
let size = 3;
for (let index = 0; index < size * size; index++) {
  arrTTT[index] = "";
}

let showModal = {
  showModal: false,
  winner: ""
};

let boardOp = {
  arr: arrTTT,
  winOp: showModal,
  player: "O"
};

function checkWinner(squares) {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6]
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return undefined;
}

function stepCorrect(cell) {
  if (cell.boardOp.winOp.winner != "" || cell.boardOp.arr[cell.num] != "")
    return false;
  else return true;
}

function nextPlayer(cell) {
  cell.boardOp.player = cell.boardOp.player == "X" ? "O" : "X";
  return cell.boardOp.player;
}

let board = Vue.component("board", {
  template: `
    <div>
      <table>
        <tr>
          <cell v-bind:num=8></cell><cell v-bind:num=7></cell><cell v-bind:num=6></cell>
        </tr>
        <tr>
          <cell v-bind:num=5></cell><cell v-bind:num=4></cell><cell v-bind:num=3></cell>
        </tr>
        <tr>
          <cell v-bind:num=2></cell><cell v-bind:num=1></cell><cell v-bind:num=0></cell>
        </tr>
      </table>
      <button-clearBoard v-on:click="clearBoard($event)">
        clear board
      </button-clearBoard>
    </div>`
});

function item1Selected() {
  alert("НЕ ГОТОВО!");
}

function item2Selected(cell) {
  Vue.set(cell.boardOp.arr, cell.num, '');
}

function item3Selected(cell) {
  doMoove(cell);
}

function doMoove(cell) {
  let isCorrect = stepCorrect(cell);

  if (isCorrect) {
    let val = nextPlayer(cell);
    Vue.set(cell.boardOp.arr, cell.num, val);

    let win = checkWinner(cell.boardOp.arr);

    if (win) {
      cell.boardOp.winOp.winner = win;
      cell.boardOp.winOp.showModal = true;
    }
  }
}

let menuOp = {
  showMenu: false,
  obj: {},
  x: 0,
  y: 0,
  items: [
    {
      img: "",
      title: "Сделать ход",
      onClose: item3Selected
    },
    {
      img: "",
      title: "Отменить последний ход",
      onClose: item1Selected
    },
    {
      img: "",
      title: "Отменить ход в этом поле",
      onClose: item2Selected
    },
  ]
};

// register popupMenu component
let popupMenuForButton = Vue.component("popupMenuForButton", {
  template: `
    <popupMenu v-bind:menu="menuOp"></popupMenu>`,
  data: function() {
    return { menuOp };
  }
});

let cell = Vue.component("cell", {
  //
  template: `<td 
              v-on:mouseup.left="handleMove($event)"
              v-on:contextmenu="show"
              v-on:contextmenu.prevent>

              {{ boardOp.arr[num] }}
            </td>`,
  props: {
    num: {
      type: Number,
      default: 0
    }
  },
  data: function() {
    return { boardOp, menuOp };
  },
  methods: {
    handleMove: function(event) {
      doMoove(this);
    },
    show(e) {
      e.preventDefault();
      this.menuOp.showMenu = false;
      this.menuOp.x = e.clientX;
      this.menuOp.y = e.clientY;
      this.$nextTick(() => {
        this.menuOp.showMenu = true;
        this.menuOp.obj = this;
      });
    }
  }
});

let buttonClearBoard = Vue.component("button-clearBoard", {
  template: `<button v-on:click="clearBoard">
              Clear board
            </button>`,
  methods: {
    clearBoard: function() {
      this.winOp.winner = "";
      this.arr = this.arr.map(el => "");
    }
  },
  data: function() {
    return boardOp;
  }
});

let buttonShowWinner = Vue.component("button-showWinnner", {
  template: `
            <div>
              <modal v-if=showModal v-on:close="showModal = false">
                <h3 slot="header">ВНИМАНИЕ, ОБНАРУЖЕН победитель!</h3>
                <h4 slot="body">Победил: {{winner}}</h4>
                <b slot="footer">НАЖМИ=></b>
              </modal>
            </div>`,
  data: function() {
    return showModal;
  },
  methods: {
    showWinner: function() {
      this.showModal = true;
    }
  }
});
</script>
