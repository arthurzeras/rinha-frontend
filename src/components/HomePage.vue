<template>
  <section class="home-page">
    <div class="home-page__content">
      <h1 class="home-page__title">JSON Tree Viewer</h1>
      <h2 class="home-page__subtitle">
        Simple JSON Viewer that runs completely on-client. No data exchange
      </h2>
      <div class="home-page__card">
        <button class="button" type="button" @click="openFileSelector">
          Load JSON
        </button>
        <input type="file" hidden ref="inputFile" @change="handleFileChange" />
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const emit = defineEmits<{
  (event: 'fileRead', result: { content: string; name: string }): void;
}>();

const inputFile = ref<HTMLInputElement | null>(null);

const openFileSelector = () => {
  inputFile.value?.click();
};

const handleFileChange = (event: Event) => {
  const files = (event.target as HTMLInputElement)?.files;

  if (!files?.length) {
    return;
  }

  const file = files[0];

  const fileReader = new FileReader();

  fileReader.addEventListener('load', (event) => {
    emit('fileRead', {
      name: file.name,
      content: event.target?.result as string,
    });
  });

  fileReader.readAsText(file);
};
</script>

<style>
.home-page {
  width: 100vw;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.home-page__content {
  text-align: center;
}

.home-page__title {
  font-size: 3.2em;
  line-height: 1.1;
}

.home-page__subtitle {
  font-weight: normal;
}

.home-page__card {
  padding: 2em;
}
</style>
