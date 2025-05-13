<script setup lang="ts">
import { computed, navigateTo, onMounted, ref, useCookie, useHead, useTemplateRef } from '#imports'
import { FetchError } from 'ofetch'
import Header from '~/components/Header.vue'
import Footer from '~/components/Footer.vue'

const lang = 'ru';

const api_host = '91.218.8.186:8003';

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
        en: "CV",
        ru: "Резюме",
    },
    cv_select_button: {
        en: "Browse",
        ru: "Выбрать",
    }
}

const api_key = useCookie<string | undefined>(
    'api_key', { default: () => undefined }
);

onMounted(async () => {
    if (api_key.value === undefined) {
        await navigateTo('/sign-in');
    }
});

const firstname = ref<string>('');
const lastname = ref<string>('');
const birthday = ref<Date>(new Date(Date.now()));
const sex = ref<'male' | 'female'>('male');
const phone_number = ref<string>('');
const phone_number_status = computed(() => {
    if (phone_number.value === '') {
        return 'empty';
    }
    if (phone_number.value.match(/^(\+7|8)/)) {
        return 'valid';
    }
    return 'invalid';
});
const cv = useTemplateRef('cv');

const submit_status = ref<'pending' | 'error' | 'success' | 'idle'>('idle');

async function submit() {
    submit_status.value = 'pending';
    let data = new FormData();
    data.append('name', firstname.value);
    data.append('surname', lastname.value);
    data.append('birthday', birthday.value.toString());
    data.append('gender', sex.value);
    data.append('phone_number', phone_number.value);
    data.append('cv', cv.value.files[0]);
    let result = await $fetch<{}>(
        `${lang}/user/info`,
        {
            method: 'POST',
            baseURL: `http://${api_host}`,
            body: data,
            query: {
                api_key: api_key.value,
            },
        }
    ).catch(async (error: FetchError) => {
        if (error.statusCode === 403) {
            api_key.value = undefined;
            await navigateTo('/sign-in');
        }
        submit_status.value = 'error';
        return null;
    });
    if (result !== null) {
        submit_status.value = 'success';
        await navigateTo('/opportunities');
    }
}

useHead({
    title: translations.page_title[lang],
})
</script>

<template>
    <Header :lang="lang" :api_host="api_host" :active_nav_section="1" :must_fill_info="false" />
    <div id="content-container">
        <div id="apply-form">
            <div class="apply-field string-field">
                <label for="">{{ translations.firstname_label[lang] }}: </label>
                <input type="text" v-model="firstname">
            </div>
            <div class="apply-field string-field">
                <label>{{ translations.lastname_label[lang] }}: </label>
                <input type="text" v-model="lastname">
            </div>
            <div class="apply-field string-field">
                <label>{{ translations.birthday_label[lang] }}: </label>
                <input type="date" v-model="birthday">
            </div>
            <div class="apply-field dropmenu-field">
                <label>{{ translations.sex_label[lang] }}: </label>
                <select v-model="sex">
                    <option value="male">Man</option>
                    <option value="female">Woman</option>
                </select>
            </div>
            <div class="apply-field">
                <label>{{ translations.phone_label[lang] }}: </label>
                <input type="text" v-model="phone_number">
                <p v-if="phone_number_status === 'invalid'">
                    Номер телефона должен начинаться с '8' или '+7'
                </p>
            </div>
            <div class="apply-field">
                <label>{{ translations.cv_label[lang] }}: </label>
                <input type="file" ref="cv" />
            </div>
            <button @click="submit" :disabled="submit_status !== 'idle' || phone_number_status !== 'valid'">
                <img v-if="submit_status === 'pending'" src="~/public/loading.gif" style="height: 2em;">
                <p v-else>Submit</p>
            </button>
            <p v-if="submit_status === 'error'">Some error occured</p>
            <p v-else-if="submit_status === 'success'">Successfully updated info</p>
        </div>
    </div>
    <Footer :lang="lang" />
</template>

<style>
@import url('~/public/css/form.css');
</style>