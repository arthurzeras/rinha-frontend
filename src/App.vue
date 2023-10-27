<template>
  <home-page @file-read="handleFileRead" v-if="!hasContent" />
  <json-tree
    v-else
    :name="fileName"
    @back="handleBack"
    :content="fileContent"
  />
  <div class="invalid-json-alert" v-if="hasError">
    <span class="invalid-json-alert__content" @click="hasError = false">
      JSON invalid, please load a valid JSON
    </span>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import HomePage from './components/HomePage.vue';
import JsonTree from './components/JsonTree.vue';

const fileName = ref('');
const hasError = ref(false);
const fileContent = ref('');
const hasContent = computed(() => !!fileContent.value.length);

const handleBack = () => {
  fileName.value = '';
  fileContent.value = '';
};

const handleFileRead = (fileInfo: { content: string; name: string }) => {
  hasError.value = false;

  try {
    JSON.parse(fileInfo.content);
  } catch (error) {
    hasError.value = true;
    return;
  }

  fileName.value = fileInfo.name;
  fileContent.value = fileInfo.content;
};
</script>

<style>
.invalid-json-alert {
  top: 15px;
  width: 100%;
  display: flex;
  position: absolute;
  align-items: center;
  justify-content: center;
}

.invalid-json-alert__content {
  color: #fff;
  padding: 15px;
  cursor: pointer;
  border-radius: 10px;
  border: 1px solid #a43939;
  background-color: #e44f4f;
  box-shadow: 0px 2px 10px rgba(0, 0, 0, 0.3);
}
</style>
