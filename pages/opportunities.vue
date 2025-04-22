<script setup lang="ts">
import { useHead } from '#imports'
import Header from '~/components/Header.vue';
import Footer from '~/components/Footer.vue';

const lang = 'ru'

const translations = {
    page_title: {
        en: 'Opportunities',
        ru: 'Возможности',
    },
    provider_info_title: {
        en: 'Provider',
        ru: 'Поставщик',
    },
    tags_title: {
        en: 'Tags',
        ru: 'Тэги',
    },
    places_title: {
        en: 'Places',
        ru: 'Места',
    },
    other_opportunities: {
        en: 'other opportunities',
        ru: 'другие возможности',
    },
    no_tags: {
        en: 'No tags available',
        ru: 'Нет доступных тэгов',
    },
    no_places: {
        en: 'No places available',
        ru: 'Нет доступных мест',
    },
    opportunity_page_button: {
        en: 'Opportunity page',
        ru: 'Страница возможности',
    }
}

const opportunity_id = 'opportunity_id'
const opportunity_title = 'Intern in the team of industrial design of Smart devices with Alice'
const provider = 'Yandex'
const category: 'Internship' | 'Job' = 'Internship'
const tags = ['Hybrid', 'Industrial design', 'User interface', 'ZXC', 'QWE']
const places: { name: string, id: string }[] = [
    { name: 'Yandex office in Moscow', id: 'yandex_office_moscow' },
]
const short_description = 'We are looking for talented interns who want to develop their skills in industrial design, work with real devices and participate in creating the future.'

useHead({
    title: `${translations.page_title[lang]} - Offer`,
})
</script>

<template>
    <Header :lang="lang" :active_nav_section="0" />
    <div id="content-container">
        <div id="opportunity-description-container">
            <div id="opportunity-title-container">
                <p>{{ opportunity_title }}</p>
                <div>
                    <a id="provider-container" :href="`/opportunities?provider=${provider}`">
                        <img src="~/public/yandex.png">
                        <p>{{ provider }}</p>
                    </a>
                    <p>/</p>
                    <a id="opportunity-category" :href="`/opportunities?category=${category}`">{{ category }}</a>
                </div>
            </div>
            <p>{{ short_description }}</p>
            <a id="visit-opportunity-page-button" :href="`/opportunity/${opportunity_id}#sections`">{{
                translations.opportunity_page_button[lang] }}</a>
        </div>
        <div id="opportunity-side-container">
            <div id="opportunity-tags-container" class="container">
                <div class="title-container">
                    <img class="img" src="~/public/hash.png">
                    <p class="title">{{ translations.tags_title[lang] }}</p>
                </div>
                <div class="tags-container">
                    <p v-if="tags.length === 0" id="tags-empty">{{ translations.no_tags[lang] }}</p>
                    <a v-for="tag in tags.slice(0, 3)" class="tag" :href="`/opportunities?tag=${tag}`">
                        <p>{{ tag }}</p>
                    </a>
                    <p v-if="tags.length > 3" id="opportunity-tag-wildcard" class="tag">...</p>
                </div>
            </div>
            <div id="opportunity-places-container" class="container">
                <div class="title-container">
                    <img class="img" src="~/public/place.png">
                    <p class="title">{{ translations.places_title[lang] }}</p>
                </div>
                <div class="places-container">
                    <p v-if="places.length === 0" id="places-empty">{{ translations.no_places[lang] }}</p>
                    <a v-for="{ name, id } in places.slice(0, 3)" class="tag" :href="`/opportunities?place=${id}`">
                        <p>{{ name }}</p>
                    </a>
                    <p v-if="places.length > 3" id="opportunity-place-wildcard" class="tag">...</p>
                </div>
            </div>
        </div>
    </div>
    <Footer :lang="lang" />
</template>

<style>
#content-container {
    height: calc(100vh - 50px);
    background-color: #141414;
    display: grid;
    grid-template-columns: 5% 60% 1fr 25% 5%;
    grid-template-rows: 5% 1fr 5%;
}

#opportunity-description-container {
    grid-column: 2 / 3;
    grid-row: 2 / 3;
    font-size: 24px;
    display: flex;
    flex-direction: column;
    gap: 30px;
    justify-content: center;
}

#opportunity-title-container {
    display: flex;
    flex-direction: column;
    gap: 5px;
    color: white;
}

#opportunity-title-container>p {
    display: -webkit-box;
    -webkit-box-orient: vertical;
    line-clamp: 2;
    -webkit-line-clamp: 2;
    font-size: 1em;
    font-weight: 450;
    overflow: hidden;
    text-overflow: ellipsis;
}

#opportunity-title-container>div {
    display: flex;
    flex-direction: row;
    justify-content: left;
    align-items: center;
    gap: 10px;
    font-size: .75em;
}

#opportunity-title-container>div>:nth-child(2n) {
    color: #555555;
    user-select: none;
}

#provider-container {
    width: fit-content;
    grid-column: 2 / 3;
    grid-row: 4 / 5;
    display: flex;
    flex-direction: row;
    justify-content: left;
    align-items: center;
    gap: 5px;
    text-decoration-color: #9e7fca;
    transition: text-decoration-color .3s;
}

#provider-container:hover {
    text-decoration-color: white;
    transition: text-decoration-color .3s;
}

#provider-container>img {
    height: 1.25em;
    aspect-ratio: 1 / 1;
}

#provider-container>p {
    font-size: 1em;
    color: white;
}

#opportunity-category {
    font-size: 1em;
    color: white;
    text-decoration-color: #9e7fca;
    transition: text-decoration-color .3s;
}

#opportunity-category:hover {
    text-decoration-color: white;
    transition: text-decoration-color .3s;
}

#opportunity-description-container>p {
    color: white;
    font-weight: 250;
}

#visit-opportunity-page-button {
    color: #d8bcff;
    align-self: center;
    text-decoration-color: #9e7fca;
    transition: text-decoration-color .3s;
}

#visit-opportunity-page-button:hover {
    text-decoration-color: white;
    transition: text-decoration-color .3s;
}

#opportunity-side-container {
    grid-column: 4 / 5;
    grid-row: 2 / 3;
    display: flex;
    flex-direction: column;
    gap: 20px;
    font-size: 24px;
}

#opportunity-side-container>.container {
    width: 100%;
    background-color: #191818;
    border: 1px solid #555555;
    border-radius: 10px;
    display: grid;
    grid-template-columns: 10px 1fr 10px;
    grid-template-rows: 10px auto 10px auto 10px;
}

#opportunity-side-container>.container>.title-container {
    grid-column: 2 / 3;
    grid-row: 2 / 3;
    display: flex;
    flex-direction: row;
    justify-content: left;
    align-items: center;
    gap: 8px;
}

.title-container>.img {
    height: 1em;
    aspect-ratio: 1 / 1;
}

.title-container>.title {
    color: white;
    font-size: .75em;
    font-weight: 450;
}

#opportunity-tags-container>.tags-container,
#opportunity-places-container>.places-container {
    grid-column: 2 / 3;
    grid-row: 4 / 5;
    display: flex;
    flex-direction: row;
    justify-content: left;
    align-items: center;
    gap: 5px;
    flex-wrap: wrap;
}

#opportunity-tags-container .tag,
#opportunity-places-container .tag {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    width: fit-content;
    height: 2em;
    background-color: #272626;
    border-radius: 5px;
    padding: 4px 6px;
    color: #b3a0cc;
    font-weight: 500;
    text-decoration: none;
    font-size: .5em;
    transition: color .3s;
}

#opportunity-tags-container .tag:hover,
#opportunity-places-container .tag:hover {
    color: white;
    transition: color .3s;
    cursor: pointer;
}

#opportunity-tag-wildcard {
    aspect-ratio: 1 / 1;
    cursor: pointer;
    user-select: none;
}

#tags-empty,
#places-empty {
    color: white;
    font-size: .75em;
}
</style>
