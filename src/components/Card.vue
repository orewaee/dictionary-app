<script setup lang="ts">
interface Props {
    en: string
    ru: string
}

const props = defineProps<Props>()
</script>

<template>
    <li class="card">
        <div class="legend">
            <strong>En</strong>
            <strong>Ru</strong>
        </div>

        <div class="border" />

        <div class="content">
            <small>{{props.en}}</small>
            <small>{{props.ru}}</small>
        </div>
    </li>
</template>

<style scoped>
.card {
    width: 264px;

    display: flex;

    background-color: var(--BG-500);

    border: 2px solid var(--BG-400);
    border-radius: 24px;
}

.legend {
    display: flex;
    flex-direction: column;

    gap: 24px;

    padding: 24px;
}

.content {
    width: 100%;

    display: flex;
    flex-direction: column;

    gap: 24px;

    padding: 24px;
}

strong {
    width: 48px;

    text-align: center;

    color: var(--FG-100);

    background-color: var(--BG-400);

    border-radius: 8px;

    user-select: none;
}

.border {
    width: 2px;
    height: auto;

    background-color: #0A0B0A;
}

small {
    text-align: center;

    color: var(--FG-300);
}
</style>
