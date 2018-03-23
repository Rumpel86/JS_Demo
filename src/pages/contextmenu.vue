<template>
    <main-layout>
        <button-showMenu></button-showMenu>
        <popupMenuForBuuton></popupMenuForBuuton>
    </main-layout>
</template>

<!-- template for the popupMenu component -->
<style>
.popupMenu-mask {
  position: absolute;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: table;
  transition: opacity 0.3s ease;
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
  border-radius: 2px;
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

<script>
import Vue from "vue";
import MainLayout from "../layouts/Main.vue";
export default {
  components: {
    MainLayout
  }
};

function closePopup(item) {
  if (item) {
    item.onClose();
  } else this.showMenu = false;
}

function item1Selected() {
  alert("1");
}

function item2Selected() {
  alert("2");
}

let menuOp = {
  showMenu: false,
  x: 0,
  y: 0,
  items: [
    {
      img: "",
      title: "command #1",
      onClose: item1Selected
    },
    {
      img: "",
      title: "command #2",
      onClose: item2Selected
    }
  ]
};

let btn = Vue.component("button-showMenu", {
  template: `
      <button v-on:click="onClickButtonShowMenu($event)" 
              v-on:contextmenu="show">
        Покажи мне меню!!!
      </button>`,
  data: function() {
    return menuOp;
  },
  methods: {
    onClickButtonShowMenu: function(event) {
      this.x = event.pageX;
      this.y = event.pageY;
      this.showMenu = true;
    },
    show(e) {
      e.preventDefault();
      this.showMenu = false;
      this.x = e.clientX;
      this.y = e.clientY;
      this.$nextTick(() => {
        this.showMenu = true;
      });
    }
  }
});

// register popupMenu component
let popupMenu = Vue.component("popupMenu", {
  template: `
      <transition name="popupMenu" v-if=showMenu>
        <div class="popupMenu-mask" @click="closePopup()">
          <div 
            class="popupMenu-wrapper"
            v-bind:style="{ left: x + 'px', top: y + 'px' }">

            <div class="popupMenu-container" v-for="item in menu.items" :key="item.title">
              <div @click="closePopup(item)">{{ item.title }}</div>
            </div>
          </div>
        </div>
      </transition>`,
  computed: {
    showMenu: {
      get: function() {
        return this.menu.showMenu;
      },
      set: function(newValue) {
        this.menu.showMenu = newValue;
      }
    },
    x: function() {
      return this.menu.x;
    },
    y: function() {
      return this.menu.y;
    }
  },
  props: {
    menu: {
      type: Object,
      default: function() {
        return {
          showMenu: false,
          x: 0,
          y: 0,
          items: [
            {
              img: "",
              title: "command #1",
              onClose: item1Selected
            },
            {
              img: "",
              title: "command #2",
              onClose: item2Selected
            }
          ]
        };
      }
    }
  },
  methods: {
    closePopup: closePopup
  }
});

// register popupMenu component
let popupMenuForBuuton = Vue.component("popupMenuForBuuton", {
  template: `
      <popupMenu v-bind:menu="menuOp"></popupMenu>`,
  data: function() {
    return { menuOp };
  }
});
</script>