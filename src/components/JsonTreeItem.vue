<template>
  <div class="json-tree-item">
    <span class="json-tree-item__title" :class="{ 'json-tree-item__array-index': isTitleANumber }">
      <span>{{ title }}:&nbsp;</span>
      <span class="json-tree-item__bracket" v-if="isContentAnArray">[</span>
    </span>

    <span v-if="isTitleANumber || isContentAnArray" class="json-tree-item__left-bar"
      :class="{ 'json-tree-item__left-bar--is-number': isTitleANumber }" />

    <div v-if="renderNewInstance">
      <json-tree-item v-for="(c, k, i) in content" :content="c" :title="k ?? i" />
    </div>

    <span v-else>
      <span v-if="primitiveContentType === 'string'">"</span>
      <span :class="contentTypeClassName">{{ content === null ? "null" : content }}</span>
      <span v-if="primitiveContentType === 'string'">"</span>
    </span>

    <span class="json-tree-item__bracket" v-if="isContentAnArray">]</span>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';

const props = defineProps<{ content: any, title: string | number }>()

const isContentAnArray = computed(() => Array.isArray(props.content))
const renderNewInstance = computed(() => {
  const content = props.content;
  return (isContentAnArray.value || typeof content === 'object') && content !== null
})
const isTitleANumber = computed(() => typeof props.title === 'number')
const primitiveContentType = computed(() => typeof props.content)
const contentTypeClassName = computed(() => {
  const map = {
    'boolean': 'json-tree-item__is-bool',
    'number': 'json-tree-item__is-number',
    'object': 'json-tree-item__is-null'
  }

  return map[primitiveContentType.value as 'number' | 'boolean' | 'object'] || ''
})
</script>

<style>
.json-tree-item {
  font-size: 1rem;
  margin-left: 1rem;
  position: relative;
  line-height: 1.5rem;
}

.json-tree-item__title {
  color: #38b5a2;
}

.json-tree-item__array-index {
  color: #888;
}

.json-tree-item__is-number {
  color: #2454d7;
}

.json-tree-item__is-bool {
  color: #de3535;
}

.json-tree-item__is-null {
  color: #aaa;
}

.json-tree-item__bracket {
  color: #ff7b00;
}

.json-tree-item__left-bar {
  left: 3px;
  width: 1px;
  top: 1.5rem;
  position: absolute;
  background-color: #ccc;
  height: calc(100% - calc(1.5rem * 2) - 5px);
}

.json-tree-item__left-bar--is-number {
  left: 3px;
  width: 1px;
  top: 1.5rem;
  position: absolute;
  background-color: #ccc;
  height: calc(100% - 1.5rem - 5px);
}
</style>