<template>
  <fwb-navbar solid>
    <template #logo>
      <fwb-navbar-logo alt="UNNES" image-url="../src/assets/unnes.png" link="/">
        Snake Ladder
      </fwb-navbar-logo>
    </template>
    <template #default="{ isShowMenu }">
      <fwb-navbar-collapse :is-show-menu="isShowMenu">
        <fwb-navbar-link link="/"> Home </fwb-navbar-link>
        <fwb-navbar-link link="/about"> About </fwb-navbar-link>
      </fwb-navbar-collapse>
    </template>
    <template #right-side>
      <router-link to="/playground">
        <fwb-button> Play Now </fwb-button>
      </router-link>
    </template>
  </fwb-navbar>
</template>

<script setup>
import { FwbButton, FwbNavbar, FwbNavbarCollapse, FwbNavbarLink, FwbNavbarLogo } from 'flowbite-vue'
</script>
