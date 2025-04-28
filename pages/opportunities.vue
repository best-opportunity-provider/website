<script setup lang="ts">
import { useHead } from '#imports'
import Header from '~/components/Header.vue';
import Footer from '~/components/Footer.vue';
import OpportunityCard from '~/components/OpportunityCard.vue';

const lang = 'ru'

const translations = {
    page_title: {
        en: 'Opportunities',
        ru: 'Возможности',
    },
}

useHead({
    title: `${translations.page_title[lang]} - Offer`,
})
</script>

<template>
    <Header :lang="lang" :active_nav_section="0" />
    <div id="content-container">
        <ClientOnly>
            <OpportunityCard
                :api_host="'localhost:8001'"
                :lang="lang"
                :opportunity_id="'123'"/>
        </ClientOnly>
    </div>
    <Footer :lang="lang" />
</template>
