<script setup lang="ts">
import PickerGrid from './PickerGrid.vue';
import PickerInput from './PickerInput.vue';

const emit = defineEmits(['change']);
</script>

<template>
  <div class="wrapper">
    <PickerGrid @change="emit('change', $event)" />
    <PickerInput @change="emit('change', $event)" />
  </div>
</template>

<style lang="scss" scoped>
.wrapper {
  margin-bottom: 3.5rem;
}
</style>
