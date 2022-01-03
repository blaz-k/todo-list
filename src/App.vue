<template>
  <Navbar />

  <router-view></router-view>

  <vdapp-board />
</template>

<script setup lang="ts">
import Navbar from './components/Navbar.vue'
</script>
