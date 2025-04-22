<script setup lang="ts">
const props = defineProps<{
    lang: 'en' | 'ru',
    active_nav_section: number | null
}>()

const nav_items = {
    'en': [
        ['Opportunities', '/opportunities'],
        ['Responses', '/responses'],
        ['Profile', '/me'],
    ],
    'ru': [
        ['Возможности', '/opportunities'],
        ['Отклики', '/responses'],
        ['Профиль', '/me'],
    ],
}
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
        <a id="header-profile-container" href="/me">
            <p>teviroff</p>
            <div class="border">
                <img src="~/public/anya.png">
            </div>
        </a>
    </header>
</template>

<style>
header {
    top: 0;
    position: sticky;
    width: 100%;
    height: 50px;
    display: grid;
    grid-template-columns: 5% auto 1fr 50% 1fr auto 5%;
    background: #101010;
    border-color: #555555;
    border-style: solid;
    border-width: 0 0 1px 0;
    font-size: 24px;
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

.header-nav-item:hover, .header-nav-item.selected {
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
</style>
