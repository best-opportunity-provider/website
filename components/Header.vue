<script setup lang="ts">

import { createError, navigateTo, useCookie, useFetch } from '#app';
import { ref, reactive } from 'vue'

const props = defineProps<{
    lang: 'en' | 'ru',
    api_host: string,
    active_nav_section: number | null,
    must_fill_info: boolean,
}>();

const api_key = useCookie<string | undefined>(
    'api_key', { default: () => undefined, maxAge: 3600 * 24 * 2 }
)

type User = {
    username: string,
    avatar: string,
    filled_info: boolean,
};

const user = ref<User | null>(null);

if (api_key.value !== undefined) {
    const { data: user_data, error } = await useFetch<User>(
        `${props.lang}/user`,
        {
            method: 'GET',
            baseURL: `http://${props.api_host}`,
            query: {
                api_key: api_key.value,
            },
        }
    );
    if (error.value !== null) {
        if (error.value.statusCode === 403) {
            api_key.value = undefined
            await navigateTo('/sign-in');
        }
        throw createError({
            statusCode: error.value.statusCode,
            statusMessage: 'Something went wrong',
            fatal: true,
        });
    }
    user.value = user_data.value;
}

if (props.must_fill_info && !user.value?.filled_info) {
    await navigateTo('/form');
}

const nav_items = {
    'en': [
        ['Opportunities', '/opportunities'],
        ['Profile', '/form'],
    ],
    'ru': [
        ['Возможности', '/opportunities'],
        ['Профиль', '/form'],
    ],
}

const menuObject = reactive({
    display: 'none',
})


function DisplayMenuBar() {
    menuObject.display = menuObject.display == 'inline' ? 'none' : 'inline'
}

const translations = {
    sign_in: {
        en: 'Sign in',
        ru: 'Войти',
    },
};
</script>

<template>
    <header>
        <a id="header-logo-container" href="/">
            <img src="~/public/yandex.png">
            <p>Offer</p>
        </a>
        <div id="header-nav-container">
            <a v-for="[item, url], index in nav_items[lang]" class="header-nav-item"
                :class="{ selected: index === active_nav_section }" :href="`${url}`">
                <p>{{ item }}</p>
            </a>
        </div>
        <!-- TODO: IF USER AUTHORIZED-->
        <a v-if="user !== null" id="header-profile-container" @click="DisplayMenuBar()">
            <p>{{ user.username }}</p>
            <div class="border">
                <img :src="`http://${props.api_host}/${props.lang}/file?id=${user.avatar}&api_key=${api_key}`">
            </div>
        </a>
        <!-- ELSE -->
        <a v-else id="header-profile-container">
            <a id="header-profile-login" href="/sign-in">{{ translations.sign_in[lang] }}</a>
        </a>
        <div :style="menuObject" id="header-drop-menu">
            <!-- <div class="header-drop-menu-item">
                <img src="~/public/settings.png">Управление аккаунтом
            </div>
            <div class="Divider"></div>

            <div class="header-drop-menu-item">
                <img src="~/public/settings.png">Управление аккаунтом
            </div>
            <div class="Divider"></div>

            <div class="header-drop-menu-item">
                <img src="~/public/settings.png">Управление аккаунтом
            </div>
            <div class="Divider"></div> -->
            <div id="logout-block">
                <div id="logout-button">Выйти</div>
            </div>
        </div>
    </header>
</template>

<style>
header {
    top: 0;
    position: sticky;
    width: 100%;
    height: 60px;
    display: grid;
    grid-template-columns: 5% auto 1fr 50% 1fr auto 5%;
    background: #101010;
    border-color: #555555;
    border-style: solid;
    border-width: 0 0 1px 0;
    font-size: 20px;
    z-index: 100;
}

#header-logo-container {
    margin: auto 0 auto 0;
    height: 80%;
    grid-column: 2 / 3;
    grid-row: 1 / 2;
    display: flex;
    flex-direction: row;
    gap: 5px;
    justify-content: left;
    align-items: center;
    text-decoration: none;
}

#header-logo-container>img {
    height: 1.6em;
    aspect-ratio: 1 / 1;
}

#header-logo-container>p {
    color: white;
}

#header-nav-container {
    margin: auto 0 auto 0;
    height: 80%;
    grid-column: 4 / 5;
    grid-row: 1 / 2;
    display: flex;
    flex-direction: row;
    gap: 50px;
    justify-content: left;
    align-items: center;
}

.header-nav-item {
    font-size: .8em;
    color: #777777;
    text-decoration: none;
    transition: color .3s;
}

.header-nav-item:hover,
.header-nav-item.selected {
    color: white;
    transition: color .3s;
}

#header-profile-container {
    margin: auto 0 auto 0;
    height: 80%;
    grid-column: 6 / 7;
    grid-row: 1 / 2;
    display: flex;
    justify-content: right;
    align-items: center;
    gap: 10px;
    color: #777777;
    text-decoration: none;
    transition: color .3s;
}

#header-profile-container:hover {
    color: white;
    transition: color .3s;
}

#header-profile-container p {
    font-size: .75em;
}

#header-profile-container img {
    height: 36px;
    max-width: 36px;
    aspect-ratio: 1 / 1;
    border-radius: 18px;
    border-color: #101010;
    border-style: solid;
    border-width: 2px;
}

#header-profile-container .border {
    height: 40px;
    width: 40px;
    background: linear-gradient(to right top, #e10e54, #2e12a8);
    border-radius: 20px;
    display: flex;
    justify-content: center;
    align-items: center;
}

#header-drop-menu {
    position: absolute;
    right: 0;
    top: 60px;
    background-color: rgb(31, 31, 36);
    border-bottom-left-radius: 10px;
    z-index: 200;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding-bottom: 15px;
    padding-top: 15px;
}

.header-drop-menu-item {
    font-size: 15px;
    display: flex;
    justify-content: left;
    padding-left: 20px;
    align-items: center;
    width: 100%;
    height: 40px;
    color: white;
    display: flex;
    gap: 5px;
    padding-right: 20px;
    border-radius: 10px;
    cursor: pointer;
}

.header-drop-menu-item img {
    height: 15px;
}

.header-drop-menu-item:hover {
    background-color: #555555;
}

#logout-button {
    background-color: rgb(160, 59, 59);
    border-radius: 10px;
    padding: 10px 25px 10px 25px;
    font-size: 15px;
    width: 100px;
}

#logout-block {
    margin-top: 20px;
    display: flex;
    justify-content: center;
    color: white;
}

#logout-button:hover {
    background-color: rgb(104, 39, 39);
    cursor: pointer;
}

.Divider {
    background: #555555;
    height: .1px;
    margin: 5px 16px;
}

#content-container {
    background-color: #141414;
    height: 100vh
}

#header-profile-login {
    border: 1px solid #777777;
    background-color: #101010;
    border-radius: 10px;
    padding: 10px 15px 10px 15px;
    color: #777777;
    font-size: 15px;
    cursor: pointer;
    text-decoration: none;
}

#header-profile-login:hover {
    color: white;
}
</style>
