<template>
  <div class="flex flex-col items-center justify-center min-h-screen text-center space-y-8">
    <fwb-heading tag="h1">About This Project</fwb-heading>
    <p class="max-w-lg text-lg">
      Proyek ini adalah implementasi digital dari permainan papan klasik Ular Tangga, yang dikembangkan sebagai bagian dari proyek universitas kami di bidang Ilmu Komputer. 
      Aplikasi ini menggunakan teori graf untuk memodelkan papan permainan, di mana setiap kotak mewakili sebuah node dan koneksi di antara mereka (misalnya, langkah, ular, dan tangga) mewakili edge.
    </p>

    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-8">
      <div class="flex flex-col items-center space-y-4">
        <img
          src="/src/assets/Febriant.jpg"
          alt="Team Member 1"
          class="w-40 h-56 rounded-lg shadow-lg object-cover"
        />
        <div>
          <h2 class="font-bold text-xl">Febriant Cahyo Nugroho</h2>
          <p class="text-sm text-gray-300">2304130097</p>
        </div>
      </div>

      <div class="flex flex-col items-center space-y-4">
        <img
          src="/src/assets/Hisma.jpg"
          alt="Team Member 2"
          class="w-40 h-56 rounded-lg shadow-lg object-cover"
        />
        <div>
          <h2 class="font-bold text-xl">Ananda Hisma Putra Kristianto</h2>
          <p class="text-sm text-gray-300">2304130100</p>
        </div>
      </div>

      <div class="flex flex-col items-center space-y-4">
        <img
          src="/src/assets/Januar.jpg"
          alt="Team Member 3"
          class="w-40 h-56 rounded-lg shadow-lg object-cover"
        />
        <div>
          <h2 class="font-bold text-xl">Januar Pancaran Nur Fajri</h2>
          <p class="text-sm text-gray-300">2304130118</p>
        </div>
      </div>

      <div class="flex flex-col items-center space-y-4">
        <img
          src="/src/assets/Aufa.jpg"
          alt="Team Member 4"
          class="w-40 h-56 rounded-lg shadow-lg object-cover"
        />
        <div>
          <h2 class="font-bold text-xl">Aufa Putra Wicaksono</h2>
          <p class="text-sm text-gray-300">2304130120</p>
        </div>
      </div>
    </div>

    <router-link to="/">
      <fwb-button class="px-8 py-4 text-xl"> Go Back </fwb-button>
    </router-link>
  </div>
</template>

<script setup>
import { FwbHeading, FwbButton } from 'flowbite-vue'
</script>
