<script setup lang="ts">
const props = defineProps<{
    lang: 'en' | 'ru'
}>()
</script>

<template>
    <footer>
        <p>©2025 Best Opportunity Provider. All Rights Reserved</p>
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
    font-size: 12px;
    display: flex;
    
    justify-content: left;
    padding-left: 50px;
    align-items: center;
    color: #777777;
}
#content-container {
    background-color: #141414;
    height: 100vh
}

</style>
