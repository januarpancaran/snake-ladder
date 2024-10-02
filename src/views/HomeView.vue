<template>
  <div class="flex flex-col items-center justify-center h-screen text-center space-y-8">
    <fwb-heading tag="h1">A Simple Snake Ladder Game</fwb-heading>
    <router-link to="playground">
      <fwb-button class="px-8 py-4 text-xl"> Play Now </fwb-button>
    </router-link>
  </div>
</template>

<script setup>
import { FwbHeading, FwbButton } from 'flowbite-vue'
</script>
