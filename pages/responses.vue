<script setup>
import { useHead } from '#imports'
import Header from '~/components/Header.vue'
import Footer from '~/components/Footer.vue'

const lang = 'ru'

const translations = {
    page_title: {
        en: 'Responses',
        ru: 'Отклики',
    },
}

useHead({
    title: translations.page_title[lang],
})
</script>

<template>
    <Header :lang="lang" :active_nav_section="null" />
    <div id="content-container">
        <div id="content-block">
            <div class="card-item">
                <div class="card-title">
                    <img src="~/public/yandex.png">
                    <label>Яндекс</label>
                </div>
                <div class="card-description">
                    <label class="card-description-name">ML-разработчик</label>
                    <label class="card-description-short-description">В музыкальный поиск Алисы</label>
                </div>
                <div class="card-status">
                    <p>Статус:</p>
                    <p class="card-status-value-ok">Одобрено</p>
                </div>
            </div>
            <div class="card-item">
                <div class="card-title">
                    <img src="~/public/yandex.png">
                    <label>Яндекс</label>
                </div>
                <div class="card-description">
                    <label class="card-description-name">ML-разработчик</label>
                    <label class="card-description-short-description">В музыкальный поиск Алисы</label>
                </div>
                <div class="card-status">
                    <p>Статус:</p>
                    <p class="card-status-value-pending">На рассмотрении</p>
                </div>
            </div>
            <div class="card-item">
                <div class="card-title">
                    <img src="~/public/yandex.png">
                    <label>Яндекс</label>
                </div>
                <div class="card-description">
                    <label class="card-description-name">ML-разработчик</label>
                    <label class="card-description-short-description">В музыкальный поиск Алисы</label>
                </div>
                <div class="card-status">
                    <p>Статус:</p>
                    <p class="card-status-value-reject">Отказано</p>
                </div>
            </div>
        </div>
    </div>
    <Footer :lang="lang"/>
</template>

<style>
@import url('~/public/css/responses.css');
</style>