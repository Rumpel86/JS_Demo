<template>
    <main-layout>
        <button-showMenu></button-showMenu>
        <popupMenu></popupMenu>
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
  transition: opacity .3s ease;
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
  transition: all .3s ease;
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

  import Vue from 'vue';
  import MainLayout from '../layouts/Main.vue'
  export default {
    components: {
      popupMenu
    }
  }

function closePopup(item) {
  if (item) {
    item.onClose(this.obj);
  } else this.showMenu = false;
}

// register popupMenu component
let popupMenu = Vue.component("popupMenu", {
  template: `
      <transition name="popupMenu" v-if=showMenu>
        <div 
        class="popupMenu-mask" 
        @mouseup="closePopup()"
        @contextmenu.prevent
        >
        
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
        return this.menu.showMenu
      },
      set: function(newValue) {
        this.menu.showMenu = newValue;
      },
    },
    x: function() {
      return this.menu.x;
    },
    y: function() {
      return this.menu.y;
    },
    obj: function() {
      return this.menu.obj;
    },
  },
  props: {
    menu: {
      type: Object,
      
    }
  },
  methods: {
    closePopup
  }
});

</script>