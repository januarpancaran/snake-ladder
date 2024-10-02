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
      <div class="flex items-center space-x-4">
        <fwb-toggle v-model="isDark" />
        <router-link to="playground">
          <fwb-button> Play Now </fwb-button>
        </router-link>
      </div>
    </template>
  </fwb-navbar>
</template>

<script setup>
import {
  FwbButton,
  FwbNavbar,
  FwbNavbarCollapse,
  FwbNavbarLink,
  FwbNavbarLogo,
  FwbToggle
} from 'flowbite-vue'
import { useDark } from '@vueuse/core'

const isDark = useDark()
</script>

<style>
html.dark {
  background-color: #16171d;
  color: #f9fafb;
}

html {
  background-color: #ffffff;
  color: #111827;
}
</style>
