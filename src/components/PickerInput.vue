<script setup lang="ts">
import { rgbToHsl, getRgbFromHex } from '@/composables/colors';
const emit = defineEmits(['change']);

const pickColor = (e: any) => {
  const color = e.target.value;
  const { r, g, b } = getRgbFromHex(color);
  const hsl = rgbToHsl(r, g, b);

  emit('change', { hsl, colorName: color });
};
</script>

<template>
  <div class="picker">
    <label for="color" class="picker-label"> Color picker: </label>
    <input id="color" class="picker-input" type="color" @input="pickColor" />
  </div>
</template>

<style lang="scss" scoped>
.picker {
  display: flex;
  align-items: center;

  &-label {
    margin-right: 1rem;
    font-weight: bold;
  }

  &-input {
    height: 2.5rem;
    flex: 1;
    border: 0;
    cursor: pointer;
  }
}
</style>
