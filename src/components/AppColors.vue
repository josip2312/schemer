<script setup lang="ts">
import { ref, watch, computed } from 'vue';
import { debounce } from 'lodash-es';
import { useClipboard } from '@vueuse/core';

import { isDark } from '@/composables/darkMode';
import colors from '@/data/variableColors';
import { Color } from '@/interfaces/Color';

interface ColorParams {
  hsl: string[];
  colorName: string;
}

let chosenHsl: string[] = [];
let copiedColor = ref('');
let variableColors = computed(() => colors);
const chosenColor = ref('default');

const { copy, copied } = useClipboard({ copiedDuring: 2500 });

watch(isDark, () => {
  // get root hsl value
  updateColor({ hsl: chosenHsl, colorName: chosenColor.value });
});

const updateColor = ({ hsl, colorName }: ColorParams) => {
  const root = document.querySelector('html');
  if (!root || !hsl.length) return;

  const [h, s, l] = hsl;

  chosenColor.value = '';
  chosenColor.value = colorName;
  root.style.setProperty('--brand-hue', h);
  root.style.setProperty('--brand-saturation', `${s}%`);
  root.style.setProperty('--brand-lightness', `${l}%`);
};

const debouncedUpdateColor = debounce(updateColor, 100);

const isTextColor = (color: Color) => {
  return color.value.includes('text');
};

const textColor = (color: Color) => {
  return isTextColor(color) ? `var(${color.value})` : color.text;
};

const copyColor = (e: any) => {
  let target = e.target;
  const BG_COLOR_ID = 'color-bg';
  const TEXT_COLOR_ID = 'color-text';

  while (!target.dataset.id) {
    target = target.parentElement;
  }

  let color = '';

  if (target.dataset.id === BG_COLOR_ID) {
    color = getComputedStyle(target).backgroundColor;
  } else if (target.dataset.id === TEXT_COLOR_ID) {
    color = getComputedStyle(target).color;
  }

  copiedColor.value = color;
  copy(color);
};
</script>

<template>
  <div class="heading">
    <AppGenerate :chosen-color="chosenColor" />
    <AppPicker @change="debouncedUpdateColor" />

    <AppTransition>
      <div v-if="copied" class="copied">Copied {{ copiedColor }}!</div>
    </AppTransition>

    <div class="brand">
      <h2 class="brand-title">Brand color:</h2>
      <div class="brand-color" :style="{ backgroundColor: 'var(--brand)' }">
        {{ chosenColor }}
      </div>
    </div>
  </div>

  <div class="colors">
    <div v-for="color in variableColors" :key="color.value" class="color">
      <div class="color-title">{{ color.name }}:</div>
      <div
        class="color-block"
        :data-id="isTextColor(color) ? 'color-text' : 'color-bg'"
        :style="{ backgroundColor: color.bg, color: textColor(color) }"
        @click="copyColor"
      >
        <div class="color-block-text">
          <span class="definition"> {{ color.value }} </span>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.heading {
  width: 100%;
  max-width: 50rem;
  margin: 0 auto;
  padding: 2.5rem 0;

  .brand {
    display: flex;
    margin-bottom: 1.5rem;

    &-title {
      margin-right: 1rem;
    }

    &-color {
      flex: 1;
      display: flex;
      align-items: center;
      justify-content: center;
      color: var(--text-on-brand);
    }
  }
}

.colors {
  width: 100%;
  max-width: 75rem;
  margin: 0 auto;
  display: grid;
  grid-template-columns: 1fr;

  @media screen and (min-width: 50rem) {
    grid-template-columns: 1fr 1fr;
    column-gap: 3rem;
  }

  .color {
    display: flex;
    align-items: center;
    margin: 1.5rem 0;

    &-title {
      font-size: 0.875rem;
      font-weight: bold;
      line-height: 1.4;
      flex: 0.3;
      margin-right: 0.5rem;
    }

    &-block {
      flex: 1;
      min-height: 2.5rem;
      padding: 0.75rem 1rem;
      cursor: pointer;

      &::after {
        content: '';
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: var(--overlay);
        opacity: 0;
        transition: opacity 150ms ease-out;
      }

      &:hover::after {
        opacity: 1;
      }

      &-text {
        .definition {
          font-size: 0.875rem;
        }
      }
    }
  }
}
</style>
