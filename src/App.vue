<script setup lang="ts">
import { RouterLink, RouterView } from 'vue-router'

const url = ref("");
</script>

<template>
        <!-- <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink> -->
        <h1>OpenChat Observer</h1>
        
  <RouterView />
</template>

<style scoped></style>