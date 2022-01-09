<script setup lang="ts">
import { isDark, toggleDark } from '@/composables/darkMode';
</script>

<template>
  <button class="btn dark-mode" @click="toggleDark()">
    Toggle {{ isDark ? 'light' : 'dark' }} mode
  </button>
</template>

<style lang="scss" scoped></style>
