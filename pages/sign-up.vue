<script setup lang="ts">
import { navigateTo, onMounted, ref, useCookie, useHead } from '#imports';
import Footer from '~/components/Footer.vue';

const lang = useCookie<'en' | 'ru'>('lang', {
    default: () => 'en',
});

const api_key = useCookie<string | undefined>(
    'api_key', { default: () => undefined }
)

const api_host = 'localhost:8001';

const username_input = ref<string>('');
const username_input_status = ref<'error' | 'valid' | 'empty'>('empty');

async function handle_username_input() {
    if (username_input.value === '') {
        username_input_status.value = 'empty';
    } else if (!username_input.value.match(/^(?!noreply)[A-Za-z\d]{1,30}$/)) {
        username_input_status.value = 'error';
    } else {
        username_input_status.value = 'valid';
    }
}

const email_input = ref<string>('');
const email_input_status = ref<'error' | 'valid' | 'empty'>('empty');

async function handle_email_input() {
    if (email_input.value === '') {
        email_input_status.value = 'empty';
    } else if (!email_input.value.match(/^((?!\.)[\w\-_.]*[^.])(@\w+)(\.\w+(\.\w+)?[^.\W])$/)) {
        email_input_status.value = 'error';
    } else {
        email_input_status.value = 'valid';
    }
}

const password_input = ref<string>('');
const password_input_status = ref<'error' | 'valid' | 'empty'>('empty');

async function handle_password_input() {
    if (password_input.value === '') {
        password_input_status.value = 'empty';
    } else if (!password_input.value.match(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[.\-@$!%*?&])[A-Za-z\d.\-@$!%*?&]*$/)) {
        password_input_status.value = 'error';
    } else {
        password_input_status.value = 'valid';
    }
}

const request_status = ref<'error' | 'pending' | 'waiting'>('waiting');

async function sign_up() {
    request_status.value = 'pending';
    let result = await $fetch<{}>(
        `${lang.value}/register`,
        {
            method: 'POST',
            baseURL: `http://${api_host}`,
            body: {
                username: username_input.value,
                email: email_input.value,
                password: password_input.value,
            },
        }
    ).catch(() => {
        request_status.value = 'error';
        return null;
    });
    if (result !== null) {
        await navigateTo('/sign-in');
    }
}

onMounted(async () => {
    if (lang.value !== 'en' && lang.value !== 'ru') {
        lang.value = 'en';
    }
    if (api_key.value !== undefined) {
        await navigateTo('/');
    }
});

const translations = {
    title: {
        en: 'Sign up',
        ru: 'Регистрация',
    },
    username: {
        en: 'Username',
        ru: 'Имя пользователя',
    },
    email: {
        en: 'Email',
        ru: 'Электронная почта',
    },
    password: {
        en: 'Password',
        ru: 'Пароль',
    },
    have_account: {
        en: 'Already have an account?',
        ru: 'Уже есть аккаунт?',
    },
    sign_up: {
        en: 'Sign up',
        ru: 'Зарегистрироваться',
    },
    sign_in: {
        en: 'Sign in',
        ru: 'Войти',
    },
    error: {
        en: 'Some error occured',
        ru: 'Произошла ошибка',
    }
};

useHead({
    title: `${translations.title[lang.value]} - Offer`,
});
</script>

<template>
    <div id="content-container">
        <div id="form-container">
            <div id="title-container">
                <p>Offer</p>
            </div>
            <div id="form-content-container">
                <div class="form-field-container">
                    <label for="form-username-field" class="form-field-label">
                        {{ translations.username[lang] }}
                    </label>
                    <input type="text" id="form-username-field" class="form-field-input" name="username"
                        placeholder="Username" v-model="username_input" @change="handle_username_input" />
                </div>
                <div class="form-field-container">
                    <label for="form-email-field" class="form-field-label">
                        {{ translations.email[lang] }}
                    </label>
                    <input type="email" id="form-email-field" class="form-field-input" name="email"
                        placeholder="email@example.com" v-model="email_input" @change="handle_email_input" />
                </div>
                <div class="form-field-container">
                    <label for="form-password-field" class="form-field-label">
                        {{ translations.password[lang] }}
                    </label>
                    <input type="password" id="form-password-field" class="form-field-input" name="password"
                        placeholder="Password" v-model="password_input" @change="handle_password_input" />
                </div>
                <button id="form-submit-button" @click="sign_up" :disabled="request_status === 'pending' || username_input_status !== 'valid' ||
                    email_input_status !== 'valid' || password_input_status !== 'valid'">
                    <p v-if="request_status !== 'pending'">
                        {{ translations.sign_up[lang] }}
                    </p>
                    <img v-else src="~/public/loading.gif" style="height: 2em;">
                </button>
                <p v-if="request_status === 'error'">
                    {{ translations.error[lang] }}
                </p>
            </div>
            <div class="form-tip-container">
                <p>{{ translations.have_account[lang] }} <NuxtLink to="/sign-in">{{ translations.sign_in[lang] }}
                    </NuxtLink>
                </p>
            </div>
        </div>
    </div>
    <Footer :lang="lang" />
</template>

<style>
@import url('~/public/css/sign.css');
</style>
