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

<style scoped>
#app {
  display: flex;
  min-height: 100dvh;
  flex-direction: column;
  justify-content: space-between;
}

.container {
  padding: 16px;
}
</style>
