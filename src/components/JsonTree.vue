<template>
  <section class="json-tree">
    <h1 class="json-tree__title">{{ name }}</h1>

    <button class="json-tree__back button" @click="$emit('back')">
      Load new JSON
    </button>

    <div class="json-tree__scroll-container" ref="treeContainer">
      <div class="json-tree__content" :style="{ height: `${totalHeight}px` }">
        <div
          v-for="(line, i) in visibleLines"
          :key="i"
          class="json-tree__line"
          :style="{
            marginLeft: `${line.tabSize}rem`,
            transform: `translateY(${offsetY}px)`,
          }"
        >
          <span
            class="json-tree__line-item"
            :class="`json-tree__line-key--${line.key.type}`"
          >
            {{ line.key.text }}
          </span>
          <span
            class="json-tree__line-item"
            :class="`json-tree__line-value--${line.value.type}`"
          >
            {{ line.value.text }}
          </span>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';

defineEmits<{
  (event: 'back'): void;
}>();

const LINE_HEIGHT = 25;

// Subtracts the padding from json-tree class and the h1 element
const CONTAINER_HEIGHT = window.innerHeight - 65 - 15;

type LineItemTypes = 'string' | 'null' | 'boolean' | 'number' | 'list-bracket';

type Line = {
  tabSize: number;
  key: { text: string; type: LineItemTypes };
  value: { text: string; type: LineItemTypes };
};

const props = defineProps<{
  name: string;
  content: string;
}>();

const treeContainer = ref<HTMLTableSectionElement | null>();
const containerScrollTop = ref(0);

onMounted(() => {
  if (treeContainer.value) {
    treeContainer.value.addEventListener('scroll', (event) => {
      const target = event.target as HTMLTableSectionElement;
      containerScrollTop.value = target.scrollTop;
    });
  }
});

const totalHeight = computed(() => lines.length * LINE_HEIGHT);
const startItem = computed(() =>
  Math.floor(containerScrollTop.value / LINE_HEIGHT),
);
const endItem = computed(
  () => Math.ceil(CONTAINER_HEIGHT / LINE_HEIGHT) + startItem.value,
);
const visibleLines = computed(() =>
  lines.slice(startItem.value, endItem.value),
);
const offsetY = computed(() => startItem.value * LINE_HEIGHT);

const formatItemTypeOutput = (
  key: string | number,
  value: any,
  mountTreeIterations: number,
): Line[] => {
  if (typeof value === 'string') {
    return [
      {
        tabSize: mountTreeIterations,
        key: { text: `${key}: `, type: typeof key as 'string' | 'number' },
        value: { text: `"${value}"`, type: 'string' },
      },
    ];
  }

  if (['boolean', 'number'].includes(typeof value)) {
    return [
      {
        tabSize: mountTreeIterations,
        key: { text: `${key}: `, type: 'string' },
        value: { text: `${value}`, type: typeof value as 'boolean' | 'number' },
      },
    ];
  }

  if (value === null) {
    return [
      {
        tabSize: mountTreeIterations,
        key: { text: `${key}: `, type: 'string' },
        value: { text: `null`, type: 'null' },
      },
    ];
  }

  if (Array.isArray(value)) {
    return [
      {
        tabSize: mountTreeIterations,
        key: { text: `${key}: `, type: 'string' },
        value: { text: '[', type: 'list-bracket' },
      },
      ...mountTree(value, mountTreeIterations + 1),
      {
        tabSize: mountTreeIterations,
        key: { text: ']', type: 'list-bracket' },
        value: { text: '', type: 'list-bracket' },
      },
    ];
  }

  return [
    {
      tabSize: mountTreeIterations,
      key: { text: `${key}:`, type: typeof key as 'number' | 'string' },
      value: { text: '', type: 'string' },
    },
    ...mountTree(value, mountTreeIterations + 1),
  ];
};

const mountTree = (
  content: any,
  iterations = 0,
  allLines: Line[] = [],
): Array<Line> => {
  if (Array.isArray(content)) {
    for (const index in content) {
      allLines.push(...formatItemTypeOutput(index, content[index], iterations));
    }

    return allLines;
  }

  for (const key of Object.keys(content)) {
    allLines.push(...formatItemTypeOutput(key, content[key], iterations));
  }

  return allLines;
};

const lines = mountTree(JSON.parse(props.content));
</script>

<style>
.json-tree {
  width: 100%;
  display: flex;
  padding-top: 15px;
  align-items: center;
  flex-direction: column;
  justify-content: center;
}

.json-tree__title {
  margin: 0;
  font-size: 25px;
  padding: 15px 0;
  line-height: 35px;
}

.json-tree__back {
  top: 15px;
  right: 15px;
  position: absolute;
}

.json-tree__scroll-container {
  width: 100%;
  overflow-y: auto;
  height: calc(100vh - 65px - 15px);
}

.json-tree__content {
  width: 50%;
  margin: 0 auto;
  font-family: 'JetBrains Mono', monospace;
}

.json-tree__line {
  gap: 0.5rem;
  height: 25px;
  display: flex;
  will-change: transform;
}

.json-tree__line-item {
  white-space: nowrap;
}

.json-tree__line-key--string {
  color: #38b5a2;
}

.json-tree__line-value--string {
  color: #000;
}

.json-tree__line-key--number {
  color: #888;
}

.json-tree__line-value--number {
  color: #2454d7;
}

.json-tree__line-value--boolean {
  color: #de3535;
}

.json-tree__line-value--null {
  color: #aaa;
}

.json-tree__line-key--list-bracket,
.json-tree__line-value--list-bracket {
  color: #ff7b00;
}
</style>
