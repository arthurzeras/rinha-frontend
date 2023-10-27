<template>
  <home-page @file-read="handleFileRead" v-if="!hasContent" />
  <json-tree :content="fileContent" :name="fileName" v-else />
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import HomePage from './components/HomePage.vue';
import JsonTree from './components/JsonTree.vue';

const fileContent = ref('');
const fileName = ref('');
const hasContent = computed(() => !!fileContent.value.length);

const handleFileRead = (fileInfo: { content: string; name: string }) => {
  fileName.value = fileInfo.name;
  fileContent.value = fileInfo.content;
};
</script>
