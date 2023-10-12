<template>
  <home-page @file-read="handleFileRead" v-if="!hasContent" />
  <div v-else>{{ fileContent }}</div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import HomePage from './components/HomePage.vue';

const fileContent = ref("")
const fileName = ref("")
const hasContent = computed(() => !!fileContent.value.length)

const handleFileRead = (fileInfo: { content: string, name: string }) => {
  fileName.value = fileInfo.name;
  fileContent.value = fileInfo.content;
}
</script>
