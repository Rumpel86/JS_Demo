<template>
  <main-layout>
    <div class="container-stopwatch">
      <div id="stopwatch">{{ formattedTime }}
        <savedTimes id="savedTimes"></savedTimes>
      </div>
      <div id="stopwatch">
        <input v-bind:value=buttonRunName id="button-run" type="button" class="myButton" v-on:click="start($event)">
        <input type="button" value="Не готово! Помедленее, я запыссую..." class="myButton" v-on:click="saveTime($event)" id="saveTimeBtn">
        <input type="button" value="Ша, уже никто никуда не спешит!" class="myButton" v-on:click="restart($event)" id="restartBtn">
      </div>
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
#savedTimes {
  font-size: 25px;
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
      buttonRunName: "Ну что, понеслись!",
      itemsSavedTime
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
    }
  }
};

let board = Vue.component("savedTimes", {
  template: `
    <div>
      <div v-for="item in itemsSavedTime" :key="item.id">
        {{ item.time }}
      </div>
    </div>`,
  data: function() {
    return { itemsSavedTime };
  }
});

let itemsSavedTime = [ {
  time: getFormattedTime(0),
  id: 0
},
 {
  time: getFormattedTime(11.123),
  id: 1
}, {
  time: getFormattedTime(22.111),
  id: 2
}];

let itemSavedTime = {
  time: getFormattedTime(0),
  id: 0
};


function saveTime(obj, e) {}

function clearStopWatch(obj, e) {
  clearTimeout(obj.timerId);
  obj.stopwatch = 0;
  obj.additionaTime = 0;
  obj.startTime = 0;
  obj.timerId = 0;
  obj.buttonRunName = "Ну что, понеслись!";
}

function startStopWatch(obj, e) {
  let buttonRunText = {
    run: "Опаньки, куда так быстро?",
    pause: "Опаньки, куда так быстро?"
  };

  if (!obj.timerId) {
    obj.startTime = 0;
    obj.buttonRunName = "Опаньки, куда так быстро?";
    timer(obj);
  } else {
    obj.stopwatch = obj.additionaTime + (new Date() - obj.startTime) / 1000;
    obj.additionaTime = obj.stopwatch;
    obj.buttonRunName = "Ну что, понеслись!";
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
