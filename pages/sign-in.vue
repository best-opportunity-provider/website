<script setup lang="ts">
import { useHead, ref, useCookie, navigateTo, useFetch } from '#imports'

const lang = useCookie<'en' | 'ru'>('lang', {
    default: () => 'en',
});

if (lang.value !== 'en' && lang.value !== 'ru') {
    lang.value = 'en';
}

const api_host = 'localhost:8001';

const api_key = useCookie<string | undefined>(
    'api_key', { default: () => undefined }
)

if (api_key.value !== undefined) {
    await navigateTo('/');
}

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

const remember_input = ref<boolean>(false);

const request_status = ref<'error' | 'pending' | 'waiting'>('waiting');

type Response = {
    api_key: string,
};

async function sign_in() {
    let { data: response } = await useFetch<Response>(`login`, {
        method: 'POST',
        baseURL: `http://${api_host}`,
        body: {
            username: username_input,
            password: password_input,
            remember_me: remember_input,
        },
        cache: 'no-cache',
    });
    if (response.value === null) {
        request_status.value = 'error';
        return;
    }
    const api_key = useCookie('api_key', {
        maxAge: remember_input.value ? 3600 * 24 * 365 : 3600 * 2
    });
    api_key.value = response.value.api_key;
    await navigateTo('/');
}

const translations = {
    page_title: {
        en: 'Sign in',
        ru: 'Авторизация',
    },
    username_error: {
        en: 'Provided username is invalid',
        ru: 'Введеное имя пользователя не валидно',
    },
    password_error: {
        en: 'Provided password is invalid',
        ru: 'Введенный пароль не валиден',
    },
    login_error: {
        en: 'Invalid combination of username and password',
        ru: 'Неверная комбинация имени пользователя и пароля',
    }
}

useHead({
    title: `${translations.page_title[lang.value]} - Offer`,
})
</script>

<template>
    <div>
        <div id="title">
            <h1>Welcome Back</h1>
            <div id="title-logo-block">
                <img src="~/public/logo.png">
                <h2>Offer</h2>
            </div>
        </div>
        <div id="container-body">
            <div id="auth-form"> <!--flex-->
                <div id="auth-form-pages">
                    <a id="login-page" class="selected-page sign-button">Login</a>
                    <a id="register-page" class="sign-button" href="/sign-up">Register</a>
                </div>
                <div id="auth-form-labels"> <!--grid-->
                    <div id="email-label">
                        <label for="email">Username</label>
                        <div id="email-label-content">
                            <img src="~/public/mail.png">
                            <input v-model="username_input" @input="handle_username_input"
                                :disabled="request_status === 'pending'" type="text" name="email"
                                placeholder="Username" />
                        </div>
                        <p v-if="username_input_status === 'error'">{{ translations.username_error[lang] }}</p>
                    </div>
                    <div id="password-label">
                        <label for="password">Password</label>
                        <div id="password-label-content">
                            <img src="~/public/password.png">
                            <input v-model="password_input" @input="handle_password_input"
                                :disabled="request_status === 'pending'" type="password" name="password"
                                placeholder="Password" />
                        </div>
                        <p v-if="password_input_status === 'error'">{{ translations.password_error[lang] }}</p>
                    </div>
                </div>
                <div id="additional">
                    <div id="additional-remember">
                        <label for="remember">Remember me</label>
                        <input v-model="remember_input" :disabled="request_status === 'pending'" type="checkbox"
                            name="remember" />
                    </div>
                    <a id="additional-label">
                        Forgot password?
                    </a>
                </div>
                <div id="auth-form-sign">
                    <button @click="async () => { request_status = 'pending', await sign_in() }"
                        :disabled="request_status === 'pending'" id="sign-in-button">
                        <template v-if="request_status !== 'pending'">
                            <img src="~/public/sign.png">
                            <p>Sign In</p>
                        </template>
                        <img v-else src="~/public/loading.gif">
                    </button>
                </div>
                <p v-if="request_status === 'error'">{{ translations.login_error[lang] }}</p>
            </div>
        </div>

    </div>
</template>

<style>
@import url('~/public/css/sign.css');
</style>
