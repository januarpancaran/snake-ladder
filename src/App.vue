<template>
  <fwb-navbar solid>
    <template #logo>
      <fwb-navbar-logo alt="UNNES" image-url="../src/assets/unnes.png" link="/">
        Snake Ladder Game
      </fwb-navbar-logo>
    </template>
    <template #default="{ isShowMenu }">
      <fwb-navbar-collapse :is-show-menu="isShowMenu">
        <fwb-navbar-link link="/"> Home </fwb-navbar-link>
        <fwb-navbar-link link="/playground"> Play </fwb-navbar-link>
        <fwb-navbar-link link="/about"> About </fwb-navbar-link>
      </fwb-navbar-collapse>
    </template>
  </fwb-navbar>
</template>

<script setup>
import { FwbNavbar, FwbNavbarCollapse, FwbNavbarLink, FwbNavbarLogo } from 'flowbite-vue'
</script>
