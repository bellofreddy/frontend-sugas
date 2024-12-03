<script setup>
import UpgradeToPro from '@/components/UpgradeToPro.vue'
</script>

<template>
  <VApp
    class="app-color"
    style="background-color:white;"
  >
    <RouterView />
    <UpgradeToPro />
    <notifications />
  </VApp>
</template>

<style lang="scss">
.app-color {
  background-color: white;
}
</style>
