<script setup lang="ts">
import { computed, ref, reactive, watch } from 'vue';
import { useClipboard } from '@vueuse/core';
import { isDark } from '@/composables/darkMode';
import colors from '@/data/variableColors';

interface Props {
  chosenColor: string;
}

const props = defineProps<Props>();
const chosenColor = reactive(props);

watch(chosenColor, () => {
  clear();
});

watch(isDark, () => {
  clear();
});

const { copy, copied } = useClipboard({ copiedDuring: 2500 });

const generatedColors = ref('');

const isGenerated = computed(() => {
  return Object.keys(generatedColors.value).length > 0;
});

function escapeRegExp(string: string) {
  return string.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
}

function replaceAll(str: string, match: string, replacement: string) {
  return str.replace(new RegExp(escapeRegExp(match), 'g'), () => replacement);
}

const getColors = () => {
  const colorData: any = {};

  const colorBlocks = Array.from(document.querySelectorAll('.color-block'));

  colors.forEach((color, i) => {
    const colorBlock = colorBlocks[i] as HTMLDivElement;

    if (colorBlock.dataset.id === 'color-bg') {
      colorData[color.value] = getComputedStyle(colorBlocks[i]).backgroundColor;
    } else {
      colorData[color.value] = getComputedStyle(colorBlocks[i]).color;
    }
  });

  return colorData;
};

const formatColors = (data: object) => {
  const newData = replaceAll(JSON.stringify(data, null, 4), '"', '');
  return replaceAll(newData, '),', ');');
};

const generateColors = () => {
  const colorData = getColors();
  const formattedColors = formatColors(colorData);

  generatedColors.value = formattedColors;
};

const clear = () => {
  generatedColors.value = '';
};
</script>

<template>
  <div class="generator">
    <button class="btn generate" @click="generateColors">Generate code</button>
    <template v-if="isGenerated">
      <button class="btn clear" @click="clear">Clear</button>
      <pre class="snippet">
        <button class="btn copy" @click="copy(generatedColors)">Copy</button>
        <code class="language-css">
          {{ generatedColors }}
        </code>
    </pre>
    </template>
    <AppTransition>
      <div v-if="copied" class="copied">Copied!</div>
    </AppTransition>
  </div>
</template>

<style lang="scss" scoped>
.generator {
  position: relative;
  width: 100%;
  margin-bottom: 2.5rem;
}

.generate {
  margin-right: 0.5rem;
  margin-bottom: 0.5rem;
}

.snippet {
  position: relative;
  background-color: var(--bg-accent-1);

  .copy {
    position: absolute;
    right: 1rem;
    top: 1rem;
  }
}
</style>
