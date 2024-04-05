<script setup>
import DifferenceChecker from './components/DifferenceChecker.vue'
import FooterNav from './components/FooterNav.vue'
import HeaderNav from './components/HeaderNav.vue'
</script>

<template>
  <HeaderNav />
  <div class="container">
    <DifferenceChecker />
  </div>
  <FooterNav />
</template>

<style>
#app {
  display: flex;
  min-height: 100dvh;
  flex-direction: column;
  justify-content: space-between;
}

.container {
  padding: 16px;
  margin: 0 auto;
  width: 100%;
  max-width: 1200px;
}
</style>
