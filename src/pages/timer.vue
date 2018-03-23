<template>
  <main-layout>
    <div class="container-stopwatch">
      <div id="stopwatch">{{ formattedTime }}</div>
      <input v-bind:value=buttonRunName id="button-run" type="button" class="myButton" v-on:click="start($event)">
      <input type="button" value="Давай сначала!" class="myButton" onclick="restart()" id="restartBtn">
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
  height: 200%;
  margin-left: auto;
  margin-right: auto;
}
#stopwatch {
  font-size: 45px;
  font-weight:bold;
  padding-left: 10px;
}
.myButton {
  border: none;
  border-radius: 5px;
  color: white;
  padding: 15px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 14px;
  font-family: Helvetica;
  margin-left: 25px;
  margin-right: 25px;
  cursor: pointer;
  -webkit-transition-duration: 0.4s;
  transition-duration: 0.4s;
  box-shadow: 0 12px 16px 0 rgba(199, 191, 191, 0.24),
    0 17px 50px 0 rgba(39, 37, 37, 0.19);
}
#button-run {
  background-color: #69f0ae;
}
#restartBtn {
  background-color: #40c4ff;
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
      time: 0,
      interval: 133,
      buttonRunName: "Ну что, понеслись!"
    };
  },
  components: {
    MainLayout
  },
  methods: {
    start: function(event) {
      startStopWatch(this, event);
    }
  }
};

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
    obj.buttonRunName = "Ну что, понеслись!";
    obj.additionaTime = obj.stopwatch;
    obj.startTime = 0;
    clearTimeout(obj.timerId);
    obj.timerId = 0;
  }
}

function getFormattedTime(time) {
  let formattedTime = "";
  if (typeof time == "undefined") {
    formattedTime = "00:00:000";
  } else {
    let min = Math.floor(time / 60);
    let sec = Math.floor(time);
    let miliseс = (time - Math.floor(time)) * 1000;

    min = ("0" + min).slice(-2);
    sec = ("0" + (sec >= 60 ? sec % 60 : sec)).slice(-2);
    miliseс = ("00" + (miliseс >= 1000 ? miliseс % 1000 : miliseс)).slice(-3);

    formattedTime = `${min} : ${sec} : ${miliseс}`;
  }
  return formattedTime;
}

function timer(obj) {
  if (!obj.startTime) obj.startTime = new Date();

  obj.timerId = setTimeout(function() {
    obj.stopwatch = obj.additionaTime + (new Date() - obj.startTime) / 1000;
    timer(obj);
  }, obj.interval);
}
</script>
