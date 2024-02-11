<script setup lang="ts">
import {BaseDirectory, createDir, exists, readTextFile, writeTextFile} from "@tauri-apps/api/fs";
import {computed, onBeforeMount, Ref, ref} from "vue";

import Card from "./components/Card.vue";

interface Word {
    en: string
    ru: string
}

const words: Ref<Word[]> = ref([]);

const query = ref("");

const result = computed(() => words.value.filter(word => {
    const lowerCaseQuery = query.value.toLowerCase();

    const a = word.en.toLowerCase().startsWith(lowerCaseQuery);
    const b = word.ru.toLowerCase().startsWith(lowerCaseQuery);

    return a || b;
}))

function shuffle() {
    words.value = words.value.map(value => ({value, sort: Math.random()}))
        .sort((a, b) => a.sort - b.sort)
        .map(({ value }) => value)
}

async function initWords() {
    const config = {dir: BaseDirectory.Document};
    const wordsPath = "Dictionary/words.json";

    try {
        await createDir("Dictionary", {
            dir: BaseDirectory.Document,
            recursive: true
        });

        if (!await exists(wordsPath, config))
            await writeTextFile(wordsPath, "[]", config);

        const json = await readTextFile(wordsPath, config);

        words.value = JSON.parse(json);
    } catch (exception) {
        console.error(exception);
    }
}

onBeforeMount(() => initWords())
</script>

<template>
    <header class="header">
        <input v-model="query" placeholder="Start typing en">
        <button @click="shuffle">
            <svg xmlns="http://www.w3.org/2000/svg" class="icon icon-tabler icon-tabler-arrows-shuffle-2" width="24" height="24" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" fill="none" stroke-linecap="round" stroke-linejoin="round">
                <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                <path d="M18 4l3 3l-3 3" />
                <path d="M18 20l3 -3l-3 -3" />
                <path d="M3 7h3a5 5 0 0 1 5 5a5 5 0 0 0 5 5h5" />
                <path d="M3 17h3a5 5 0 0 0 5 -5a5 5 0 0 1 5 -5h5" />
            </svg>
        </button>
    </header>

    <TransitionGroup tag="div" name="fade" class="cards">
        <Card v-for="word in result" :key="word.en" :en="word.en" :ru="word.ru" />
    </TransitionGroup>
</template>

<style scoped>
.header {
    z-index: 1;

    position: sticky;
    top: 0;

    width: 100%;

    display: flex;
    justify-content: space-between;

    gap: 24px;

    background-color: rgba(0, 0, 0, .48);
    backdrop-filter: blur(12px);

    padding: 24px;
}

.header input {
    width: 100%;
    height: 48px;

    color: var(--FG-300);

    border: 2px solid var(--BG-400);
    border-radius: 12px;

    background-color: var(--BG-500);

    outline: none;

    padding: 12px 24px;
}

.header input::placeholder {
    color: var(--FG-100);
}

.header button {
    width: 48px;
    height: 48px;

    display: flex;
    align-items: center;
    justify-content: center;

    color: var(--FG-100);

    border: 2px solid var(--BG-400);
    border-radius: 12px;

    background-color: var(--BG-500);

    cursor: pointer;

    transition: 240ms ease;
}

.header button:hover {
    background-color: var(--BG-400);
}

.cards {
    position: relative;

    display: flex;
    flex-wrap: wrap;

    gap: 24px;

    padding: 0 24px 24px;
}

.fade-move,
.fade-enter-active,
.fade-leave-active {
    transition: all 240ms ease;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
    transform: scale(0);
}

.fade-leave-active {
    position: absolute;
}
</style>
