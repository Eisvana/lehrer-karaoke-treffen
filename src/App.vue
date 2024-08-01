<script setup lang="ts">
import { ref } from 'vue';

const name = ref('');

const webhook: string = atob(import.meta.env.VITE_DISCORD_WEBHOOK ?? '');

async function sendWebhook(appears: boolean) {
  await fetch(webhook, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      content: `${name.value} erscheint${appears ? '' : ' nicht'}.`,
    }),
  });
}
</script>

<template>
  <h1>Hello Gregor!</h1>
  <p class="me">Ich,</p>
  <input
    v-model="name"
    placeholder="Name"
    type="text"
  />
  <div class="button-wrapper">
    <button
      :disabled="!name"
      @click="sendWebhook(true)"
    >
      erscheine 🦈
    </button>
    <button
      :disabled="!name"
      class="secondary"
      @click="sendWebhook(false)"
    >
      erscheine nicht
    </button>
  </div>
</template>

<style scoped>
.secondary:not(:hover) {
  background-color: #647378;
}

h1,
.me {
  text-align: center;
}

h1 {
  margin-block-end: 2rem;
}

.button-wrapper {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
  justify-content: center;
}
</style>
