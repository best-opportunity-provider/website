<script setup>
import { useHead } from '#imports'
import { ref } from 'vue'

useHead({
    title: 'Welcome to Offer',
})
const counter = ref(0)
</script>

<template>
    <p>Button was clicked {{ counter }} times.</p>
    <button v-on:click="++counter">Click me!</button>
</template>
