<template>
  <main-layout>
    
    <h2>ВНИМАНИЕ, ДОБАВЛЕНА ВОЗМОЖНОСТЬ УПРАВЛЕНИЯ ЖЕСТАМИ!</h2>
    <p>Жесты должны быть четкими, сложный анализ погрешности не вводил</p>
    <p>Жест "Вниз-Вверх": старт или стоп</p>
    <p>Жест "Вправо-Влево": сохранить время</p>
    <p>Жест "Вниз0=-Влево": обнулить</p>
    <div class="container-stopwatch"  
    v-on:mousedown.left="eventMouseDown($event)"
    v-on:mouseup.left="eventMouseUp($event)"
    v-on:mousemove="eventMouseMove($event)"
    >
    <canvas id='canvas'></canvas>
     <div id="stopwatch">
        <input v-bind:value=buttonRunName id="button-run" type="button" class="myButton" v-on:click="start($event)">
        <input type="button" value="Сохранить время" class="myButton" v-on:click="saveTime($event)" id="saveTimeBtn">
        <input type="button" value="Обнулить" class="myButton" v-on:click="restart($event)" id="restartBtn">
      </div>
      <div id="stopwatch">{{ formattedTime }}
        <savedTimes id="savedTimes"></savedTimes>
      </div>
      <div id="stopwatch111">r:{{ route }}</div>
      <div id="stopwatch111">rX: {{ routeX }}</div>
      <div id="stopwatch111">rY: {{ routeY }}</div>
      <div id="stopwatch111">dX: {{ deltaX }}</div>
      <div id="stopwatch111">dY: {{ deltaY }}</div>
      <div id="stopwatch111">route: {{ routeP }}</div>
    </div>
  </main-layout>
</template>

<style>
.container-stopwatch {
  background-color: #37474f;
  border: solid;
  border-radius: 10px;
  border-color: #eceff1;
  padding-bottom: 30px;
  width: 90%;
  height: 500px;
  margin-left: auto;
  margin-right: auto;
}
#stopwatch {
  font-size: 45px;
  font-weight: bold;
  padding-left: 10px;
  padding-top: 10px;
  display: inline-block;
}
#horizontal-location {
  font-size: 45px;
  font-weight: bold;
  padding-left: 10px;
  padding-top: 10px;
  display: inline-block;
}
#savedTimes {
  font-size: 25px;
  color: dimgrey;
  font-weight: bold;
  padding-left: 10px;
  padding-top: 10px;
}
.myButton {
  border: none;
  border-radius: 5px;
  color: black;
  padding: 5px;
  text-align: center;
  text-decoration: none;
  display: block;
  font-size: 14px;
  font-family: Helvetica;
  margin-left: 25px;
  margin-right: 25px;
  cursor: pointer;
  -webkit-transition-duration: 0.4s;
  transition-duration: 0.4s;
  box-shadow: 0 12px 16px 0 rgba(199, 191, 191, 0.24),
    0 17px 50px 0 rgba(39, 37, 37, 0.19);
  width: 250px;
}
#button-run {
  background-color: #69f0ae;
}
#saveTimeBtn {
  background-color: #40c4ff;
}
#restartBtn {
  background-color: #eaff32;
}
</style>

<script>
import Vue from "vue";
import MainLayout from "../layouts/Main.vue";

export default {
  computed: {
    formattedTime: {
      get: function() {
        return getFormattedTime(this.stopwatch);
      }
    }
  },
  data() {
    return {
      stopwatch: 0,
      additionaTime: 0,
      timerId: 0,
      interval: 133,
      route: 133,
      routeX: 133,
      routeY: 133,
      deltaX: 133,
      deltaY: 133,
      wayFuncs,
      routeP: "",
      buttonRunName: "СТАРТ",
      mouseDown: {
        down: false,
        event: undefined,
        func: [],
        preFunc: []
      },
      data,
      body: {
        canvas: document.getElementById("canvas"),
        paintStarted: false
      }
    };
  },
  components: {
    MainLayout
  },
  methods: {
    start: function(event) {
      startStopWatch(this, event);
    },
    restart: function(event) {
      clearStopWatch(this, event);
    },
    saveTime: function(event) {
      saveTime(this, event);
    },
    eventMouseDown: function(event) {
      this.mouseDown.down = true;
      this.mouseDown.event = event;
    },
    eventMouseMove: function(event) {
      function determinateRouting(delta, limit, limitError) {
        let route = undefined;
        let a = Math.round(Math.abs(delta) / limit * 10) / 10;
        if (a > 1 - limitError) route = delta < 0 ? -1 : 1;
        else if (a > -limitError && a < limitError) route = 0;
        return route;
      }

      if (event.buttons === 1) {
        this.mouseDown.event = this.mouseDown.event || event;
        event.preventDefault();
        let limit = 20;
        let limitError = 0.35;
        let maxCount = 3;

        let deltaX = this.mouseDown.event.screenX - event.screenX;
        let deltaY = this.mouseDown.event.screenY - event.screenY;
        let routeX = determinateRouting(deltaX, limit, limitError);
        let routeY = determinateRouting(deltaY, limit, limitError);
        let route = "";

        if (routeX == 0 && routeY == 1) route = "n";
        else if (routeX == 0 && routeY == -1) route = "s";
        else if (routeX == 1 && routeY == 0) route = "w";
        else if (routeX == -1 && routeY == 0) route = "e";
        else if (routeX == 1 && routeY == 1) route = "nw";
        else if (routeX == 1 && routeY == -1) route = "sw";
        else if (routeX == -1 && routeY == 1) route = "ne";
        else if (routeX == -1 && routeY == -1) route = "se";

        this.route = route;
        this.routeX = routeX;
        this.routeY = routeY;
        this.deltaX = deltaX;
        this.deltaY = deltaY;

        if (route != "") {

          if (!this.body.paintStarted) {
            //debugger;

            console.log('Begin');
            //debugger;
            this.body.paintStarted = true;
            this.body.canvas = document.getElementById("canvas");
            this.body.ctx = this.body.canvas.getContext('2d');
        
            this.body.ctx.lineWidth = 3;
            this.body.ctx.strokeStyle = 'rgb(255, 220, 220)';
            this.body.ctx.beginPath();
            this.body.ctx.moveTo(event.screenX - this.body.canvas.offsetLeft, event.screenY - this.body.canvas.offsetTop-130);
            
            
          }
          
          //debugger;
          this.body.ctx.lineTo(event.screenX - this.body.canvas.offsetLeft, event.screenY - this.body.canvas.offsetTop-130);
          this.body.ctx.stroke();

          this.mouseDown.event = event;

          let objFunc = {
            route,
            counte: 0,
            routeX,
            routeY,
            deltaX,
            deltaY
          };

          // if (preFunc == undefined || preFunc.route != route) {
          //   this.mouseDown.preFunc = [];
          //   this.mouseDown.preFunc.push(objFunc);
          // } else if (preFunc.counte >= maxCount) {
          //   let func = this.mouseDown.func[this.mouseDown.func.length - 1];
          //   if (func == undefined || func.route != preFunc.route) {
          //     this.mouseDown.func.push(objFunc);
          //     this.routeP = new String(route);
          //   }
          // } else preFunc.counte++;

          // if (preFunc == undefined || preFunc.route != route) {
          //   this.mouseDown.preFunc = [];
          //   this.mouseDown.preFunc.push(objFunc);
          // } else if (preFunc.counte >= maxCount) {
          //   let func = this.mouseDown.func[this.mouseDown.func.length - 1];
          //   if (func == undefined || func != preFunc.route) {
          //     this.mouseDown.func.push(route);
          //     this.routeP = new String(route);
          //   }
          // } else preFunc.counte++;

          this.mouseDown.preFunc = this.mouseDown.preFunc.slice(-maxCount+1);
          this.mouseDown.preFunc.push(route);
          let countRoute = 0;
          for (let i = 0; i < this.mouseDown.preFunc.length; i++) {
            if ((this.mouseDown.preFunc[i] = route)) countRoute++;

            if (countRoute >= maxCount) break;
          }

          if (countRoute >= maxCount) {
            let func = this.mouseDown.func[this.mouseDown.func.length - 1];
            if (func == undefined || func != route) {
              this.mouseDown.preFunc = [];
              this.mouseDown.func.push(route);
              this.routeP = new String(route);
            }
          }
        }
      }
    },
    eventMouseUp: function(event) {
      let way = this.mouseDown.func.join();
      let wayFunc = this.wayFuncs[way];

      this.body.paintStarted = false;

      if (wayFunc) {
        //debugger;
        wayFunc(this, event);
      }

      console.log(this.mouseDown.func.join());
      this.mouseDown.down = false;
      this.mouseDown.event = undefined;
      this.mouseDown.func = [];

      this.body.ctx.clearRect(0,0,this.body.canvas.width,this.body.canvas.height);
    }
  }
};

let board = Vue.component("savedTimes", {
  template: `
    <div class="horizontal-location">
      <div v-for="item in data.itemsSavedTime" :key="item.id">
        {{ item.time }}
        <button v-bind:itemId=item.id  v-on:click="dellSavedTime($event, item.id)">УДАЛИТЬ!</button>
      </div>
    </div>`,
  data: function() {
    return { data };
  },
  methods: {
    dellSavedTime: function(event, id) {
      this.data.itemsSavedTime = this.data.itemsSavedTime.filter(el => {
        return el.id != id;
      });
    }
  },
  props: {
    itemId: {
      type: Number,
      default: 0
    }
  }
});

let data = { itemsSavedTime: [] };
let wayFuncs = {
  "s,n": startStopWatch,
  "e,w": saveTime,
  "s,w": clearStopWatch
};

function saveTime(obj, e) {
  if (!!obj.timerId) {
    obj.stopwatch = obj.additionaTime + (new Date() - obj.startTime) / 1000;
  }

  let itemSavedTime = {
    time: getFormattedTime(obj.stopwatch),
    id: obj.data.itemsSavedTime.length + 1
  };
  obj.data.itemsSavedTime.push(itemSavedTime);
}

function clearStopWatch(obj, e) {
  clearTimeout(obj.timerId);
  obj.stopwatch = 0;
  obj.additionaTime = 0;
  obj.startTime = 0;
  obj.timerId = 0;
  obj.buttonRunName = "Ну что, понеслись!";
  obj.data.itemsSavedTime = [];
}

function startStopWatch(obj, e) {
  let buttonRunText = {
    run: "СТОП",
    pause: "СТОП"
  };

  if (!obj.timerId) {
    obj.startTime = 0;
    obj.buttonRunName = "СТОП";
    timer(obj);
  } else {
    obj.stopwatch = obj.additionaTime + (new Date() - obj.startTime) / 1000;
    obj.additionaTime = obj.stopwatch;
    obj.buttonRunName = "СТАРТ";
    obj.startTime = 0;
    clearTimeout(obj.timerId);
    obj.timerId = 0;
  }
}

function getFormattedTime(time) {
  let formattedTime = "";
  time = Math.round(time * 1000) / 1000;
  if (typeof time == "undefined") {
    formattedTime = "00:00:000";
  } else {
    let min = Math.floor(time / 60);
    let sec = Math.floor(time);
    let miliseс = Math.floor((time - Math.floor(time)) * 1000);

    min = ("0" + min).slice(-2);
    sec = ("0" + (sec >= 60 ? sec % 60 : sec)).slice(-2);
    miliseс = ("00" + (miliseс >= 1000 ? miliseс % 1000 : miliseс)).slice(-3);

    formattedTime = `${min} : ${sec} : ${miliseс}`;
  }
  return formattedTime;
}

function timer(obj) {
  if (!obj.startTime) obj.startTime = new Date();

  obj.timerId = setTimeout(
    function(cont) {
      if (cont.timerId) {
        cont.stopwatch =
          cont.additionaTime + (new Date() - cont.startTime) / 1000;
        timer(cont);
      }
    },
    obj.interval,
    obj
  );
}
</script>
