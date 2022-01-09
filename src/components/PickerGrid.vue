<script setup lang="ts">
import { Color } from '@/interfaces/Color';
import { rgbToHsl, getRgbFromHex } from '@/composables/colors';
import colors from '@/data/pickerColors';

const emit = defineEmits(['change']);

const pickFixedColor = (color: Color) => {
  const { r, g, b } = getRgbFromHex(color.value);
  const hsl = rgbToHsl(r, g, b);

  emit('change', { hsl, colorName: color.name });
};
</script>

<template>
  <div class="picker-colors">
    <div
      v-for="color in colors"
      :key="color.name"
      class="picker-color"
      :style="{ backgroundColor: color.value }"
    >
      <input
        :id="color.name"
        class="picker-color-input"
        name="color"
        type="radio"
        :value="color.value"
        @change="pickFixedColor(color)"
      />
      <label class="picker-color-option" :for="color.name">{{
        color.name
      }}</label>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.picker-colors {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(10rem, 1fr));
  margin-bottom: 2.5rem;

  .picker-color {
    display: block;
    width: 100%;

    &-input {
      display: none;
    }

    &-option {
      display: flex;
      align-items: center;
      justify-content: center;
      width: 100%;
      height: 100%;
      padding: 1rem 0;
      cursor: pointer;
    }

    &:hover {
      opacity: 0.9;
    }
  }
}
</style>
