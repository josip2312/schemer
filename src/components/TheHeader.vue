<script setup lang="ts"></script>

<template>
  <header class="header">
    <div class="logo">Schemer</div>
    <DarkMode />
  </header>
</template>

<style lang="scss" scoped>
.header {
  position: sticky;
  background: var(--bg);
  z-index: 100;
  top: 0;
  display: flex;
  align-items: center;

  .logo {
    font-size: 1.15rem;
    text-decoration: none;
    color: inherit;
  }

  .dark-mode {
    margin-left: auto;
  }
}
</style>
