<script setup>
import { useHead } from '#imports'
import Header from '~/components/Header.vue'
import Footer from '~/components/Footer.vue'

const lang = 'ru'

const translations = {
    page_title: {
        en: 'Welcome to Offer',
        ru: 'Добро пожаловать на Offer',
    },
}

useHead({
    title: translations.page_title[lang],
})
</script>

<template>
    <Header :lang="lang" :active_nav_section="null" />
    <div id="content-container">
        <p>Lorem Ipsum</p>
    </div>
    <Footer :lang="lang"/>
</template>

<style>
#content-container {
    background-color: #141414;
    height: 100vh
}
</style>
