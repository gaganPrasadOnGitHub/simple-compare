<template>
  <div class="header-nav">
    <h2 class="title"><span class="fw-300">Simple</span> Compare</h2>
    <img
      class="cursor-pointer theme-icon"
      :src="theme === 'dark' ? day : night"
      alt="theme"
      @click="toggleTheme"
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import day from '../assets/day.svg'
import night from '../assets/night.svg'

const theme = ref('light')

const toggleTheme = () => {
  theme.value = theme.value === 'light' ? 'dark' : 'light'
  document.documentElement.setAttribute('data-theme', theme.value)
}

const setInitialTheme = () => {
  const hour = new Date().getHours()
  theme.value = hour >= 7 && hour < 19 ? 'light' : 'dark'
  document.documentElement.setAttribute('data-theme', theme.value)
}

onMounted(setInitialTheme)
</script>

<style scoped>
.header-nav {
  padding: 16px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.title {
  font-weight: 600;
  color: var(--main-color);
  font-family: 'DM Serif Display', serif;
}

.theme-icon {
  width: 40px;
  height: 40px;
}
</style>
