<script setup>
import { useHead } from '#imports'
import Header from '~/components/Header.vue'
import Footer from '~/components/Footer.vue'

const lang = 'ru'

const translations = {
    page_title: {
        en: 'Profile',
        ru: 'Профиль',
    },
}

useHead({
    title: translations.page_title[lang],
})
</script>

<template>
    <Header :lang="lang" :active_nav_section="null" />
    <div id="content-container">
        <div id="profile-container">
            <div id="info-container">
                <div id="avatar-container">
                    <img src="~/public/anya.png">
                </div>
                <div id="credentials-container">
                    <div id="name-container">
                        <p>Максим Эйхман</p>
                        <img src="~/public/settings.png">
                    </div>
                    <ul id="phone-container">
                        <li>+7 800 535-35-35</li>
                        <li>Flamelitonixik</li>
                    </ul>
                </div>
            </div>
            <div id="cv-container">
                <div id="title-cv-block">
                    <p class="title-block">Резюме</p>
                    <!-- <img src="~/public/arrow.png"> -->
                </div>
                <div id="cv-list-block">
                    <div class="cv-block">
                        <div class="cv-block-rect">
                            <img>
                        </div>
                        <p>Some.cv</p>
                    </div>
                    <div class="cv-block">
                        <div class="cv-block-rect">
                            <img>
                        </div>
                        <p>Some.cv</p>
                    </div>
                    <div class="cv-block">
                        <div class="cv-block-rect">
                            <img>
                        </div>
                        <p>Some.cv</p>
                    </div>
                    <div class="cv-block">
                        <div class="cv-block-rect">
                            <img>
                        </div>
                        <p>Some.cv</p>
                    </div>
                </div>
            </div>
            <div id="geo-container">
                <div id="title-geo-block">
                    <p class="title-block">Адреса</p>
                </div>
                <div id="geo-list-block">
                    <div class="geo-block">
                        <div class="geo-block-rect">
                            <img>
                            <p>Локация 1</p>
                        </div>
                    </div>
                    <div class="geo-block">
                        <div class="geo-block-rect">
                            <img>
                            <p>Локация 2</p>
                        </div>
                    </div>
                    <div class="geo-block">
                        <div class="geo-block-rect">
                            <img>
                            <p>Локация 2</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <Footer :lang="lang"/>
</template>

<style>
@import url('~/public/css/profile.css');
</style>