<template>
  <div class="container">
    <ul>
      <li>
        <v-link href="/">Home</v-link>
        <v-link href="/about">About</v-link>
        <v-link href="/tic-tac-toe">tic-tac-toe</v-link>
        <v-link href="/contextmenu">contextmenu</v-link>
        <v-link href="/timer">timer</v-link>
      </li>
    </ul>

    <slot></slot>
  </div>
</template>

<script>
  import VLink from '../components/VLink.vue'

  export default {
    components: {
      VLink
    }
  }
</script>

<style scoped>
  .container {
    margin: 0 auto;
    padding: 15px 30px;
    background: #f9f7f5;
    
  }
</style>
