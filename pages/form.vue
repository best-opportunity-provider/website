<script setup>
import { useHead } from '#imports'
import Header from '~/components/Header.vue'
import Footer from '~/components/Footer.vue'

const lang = 'ru'

const translations = {
    page_title: {
        en: 'Form',
        ru: 'форма',
    },
    firstname_label: {
        en: "Firstname",
        ru: "Имя",
    },
    lastname_label: {
        en: "Lastname",
        ru: "Фамилия",
    },
    birthday_label: {
        en: "Birthday",
        ru: "День рождения",
    },
    sex_label: {
        en: "Sex",
        ru: "Пол",
    },
    email_label: {
        en: "Email",
        ru: "Почта",
    },
    phone_label: {
        en: "Phone",
        ru: "Телефон",
    },
    cv_label: {
        en: "Cv",
        ru: "Резюме",
    },
    cv_select_button: {
        en: "Browse",
        ru: "Выбрать",
    }
}

useHead({
    title: translations.page_title[lang],
})
</script>

<template>
    <Header :lang="lang" :active_nav_section="null" />
    <div id="content-container">
        <div id="apply-form">
            <div class="apply-field string-field">
                <label for="">{{ translations.firstname_label[lang] }}: </label>
                <input type="text">
            </div>
            <div class="apply-field string-field">
                <label>{{ translations.lastname_label[lang] }}: </label>
                <input type="text">

            </div>
            <div class="apply-field string-field">
                <label>{{ translations.birthday_label[lang] }}: </label>
                <input type="date">
            </div>
            <div class="apply-field dropmenu-field">
                <label>{{ translations.sex_label[lang] }}: </label>
                <select>
                    <option>- not selected -</option>
                    <option>Man</option>
                    <option>Woman</option>
                </select>
            </div>
            <div class="apply-field">
                <label>{{ translations.email_label[lang] }}: </label>
                <input type="text">

            </div>
            <div class="apply-field">
                <label>{{ translations.phone_label[lang] }}: </label>
                <input type="text">
            </div>
            <div class="apply-field">
                <label>{{ translations.cv_label[lang] }}: </label>
                <input type="file" id="selectedFile" style="display: none;" />
                <input type="button" :value="translations.cv_select_button[lang]" onclick="document.getElementById('selectedFile').click();" />
            </div>
        </div>

    </div>
    <Footer :lang="lang"/>
</template>

<style>
@import url('~/public/css/form.css');
</style>