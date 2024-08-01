<script setup lang="ts">
import { computed, ref } from 'vue';

const paramsString = window.location.search;
const searchParams = new URLSearchParams(paramsString);
const targetName = searchParams.get('name') ?? '';

const name = ref(targetName);

const webhook: string = atob(import.meta.env.VITE_DISCORD_WEBHOOK ?? '');

const staysOnPage = ref(false);

const currentUrl = window.location.href;

async function sendWebhook(appears: boolean) {
  staysOnPage.value = !appears;
  await fetch(webhook, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      content: `${name.value} erscheint${appears ? '! 🦈' : ' nicht.'}`,
    }),
  });
  if (!appears) return;
  window.location.assign('https://gregor.pen-pixels.de');
}

const buttons = [
  {
    text: 'erscheine 🦈',
    appears: true,
  },
  {
    text: 'erscheine nicht',
    appears: false,
  },
];

const heading = computed(() => (targetName ? `Hallo ${targetName}!` : 'Zu-/Absage'));
</script>

<template>
  <h1 class="text-center">{{ heading }}</h1>
  <main
    v-if="!staysOnPage"
    class="page"
  >
    <p>Ich,</p>
    <input
      v-model="name"
      placeholder="Name"
      type="text"
    />
    <div class="button-wrapper">
      <button
        v-for="button in buttons"
        :aria-disabled="!name || undefined"
        :class="{ secondary: !button.appears }"
        :disabled="!name || undefined"
        @click.prevent="sendWebhook(button.appears)"
      >
        {{ button.text }}
      </button>
    </div>
    <p class="text-center text-balanced date">am 16.08.2024 um 15:00 Uhr in Leipzig am Augustusplatz</p>
  </main>
  <main
    v-else
    class="page"
  >
    <p>Schade, dass Sie nicht dabei sind.</p>
    <a
      :href="currentUrl"
      role="button"
      >Umentscheiden 🦈</a
    >
  </main>
</template>

<style scoped>
.secondary:not(:hover) {
  background-color: #647378;
}

.text-center {
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

.date {
  margin-block-start: 0.5rem;
}

.text-balanced {
  text-wrap: balance;
}

.page {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
