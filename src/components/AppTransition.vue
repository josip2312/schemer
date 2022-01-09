<script setup lang="ts"></script>

<template>
  <transition mode="out-in" name="fade">
    <slot />
  </transition>
</template>

<style lang="scss" scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 150ms ease-out;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
