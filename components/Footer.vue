<script setup lang="ts">
const props = defineProps<{
    lang: 'en' | 'ru'
}>()
</script>

<template>
    <footer>
        <p>This is a footer</p>
    </footer>
</template>

<style>
footer {
    width: 100%;
    height: 50px;
    background: #101010;
    border-color: #555555;
    border-style: solid;
    border-width: 1px 0 0 0;
    font-size: 24px;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #777777;
}
</style>
