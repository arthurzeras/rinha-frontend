<template>
  <section class="json-tree">
    <h1>{{ name }}</h1>

    <div class="json-tree__content">
      <json-tree-item v-for="(content, b, i) in contentParsed" :content="content" :title="i || b" :key="b || i" />
    </div>
  </section>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import JsonTreeItem from './JsonTreeItem.vue';

const props = defineProps<{
  name: string,
  content: string
}>()

const contentParsed = computed(() => JSON.parse(props.content))
</script>

<style>
.json-tree {
  width: 50%;
  margin: 0 auto;
  padding-top: 1rem;
}

.json-tree__content {
  font-family: "JetBrains Mono", monospace;
}
</style>