<template>
  <div>
    <div v-for="item in itemsSavedTime" :key="item.id">
      {{ item.time }}
    </div>
  </div>
</template>

<style>

</style>

<script>
import Vue from "vue";
import MainLayout from "../layouts/Main.vue";

export default {
  components: {
    MainLayout
  },
};


</script>
