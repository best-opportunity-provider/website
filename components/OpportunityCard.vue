<script setup lang="ts">
import { createError, useCookie, useLazyFetch } from '#app';

const props = defineProps<{
    lang: 'en' | 'ru',
    api_host: string,
    opportunity_id: string,
}>();

type NamedObject = {
    id: string,
    name: string,
};

type Category = 'internship' | 'job';

type Opportunity = {
    id: string,
    title: string,
    description: string,
    provider: NamedObject & {
        logo: string,  // URL
    },
    category: Category,
    tags: NamedObject[],
    places: NamedObject[],
};

function capitalize(s: string): string {
    return s[0].toUpperCase() + s.slice(1);
}

const api_key = useCookie<string | undefined>(
    'api_key', { default: () => undefined }
)

// if (!api_key.value) {
//     throw createError({
//         statusCode: 403,
//         statusMessage: 'Authentication required',
//         fatal: true,
//     });
// }

const { data: opportunity, status: load_status } = await useLazyFetch<Opportunity>(
    `${props.lang}/opportunity`,
    {
        method: 'GET',
        baseURL: `http://${props.api_host}`,
        query: {
            opportunity_id: props.opportunity_id,
            api_key: api_key.value,
        },
    }
);

// const opportunity: Opportunity = {
//     id: '67f39fbb08c63243c50dd6fc',
//     title: 'Intern in the team of industrial design of Smart devices with Alice',
//     description: 'We are looking for talented interns who want to develop their skills in industrial design, work with real devices and participate in creating the future.',
//     provider: {
//         id: '67f39fbb08c63243c50dd6fc',
//         name: 'Yandex',
//         logo: '/yandex.png',
//     },
//     category: 'internship',
//     tags: [
//         { id: 'format_hybrid', name: 'Hybrid' },
//         { id: 'branch_industrial_design', name: 'Industrial design' },
//         { id: 'branch_user_interface', name: 'User interface' },
//     ],
//     places: [
//         { id: '67f39fbb08c63243c50dd6fc', name: 'Yandex office in Moscow' },
//     ],
// };

const translations = {
    visit_page_button: {
        en: 'Opportunity page',
        ru: 'Страница возможности',
    },
    tags: {
        en: 'Tags',
        ru: 'Теги',
    },
    places: {
        en: 'Places',
        ru: 'Места',
    },
    no_tags: {
        en: 'No tags available',
        ru: 'Нет доступных тегов',
    },
    no_places: {
        en: 'No places available',
        ru: 'Нет доступных мест',
    },
    head_error: {
        en: 'Couldn\'t load opportunity',
        ru: 'Ошибка загрузки возможности',
    },
    tags_error: {
        en: 'Couldn\'t load tags',
        ru: 'Ошибка загрузки тегов',
    },
    places_error: {
        en: 'Couldn\'t load places',
        ru: 'Ошибка загрузки мест',
    },
}
</script>

<template>
    <div id="opportunity-head-container">
        <div v-if="load_status === 'pending'" id="opportunity-head-pending-container">
            <img src="~/public/loading.gif" class="loader">
        </div>
        <div v-else-if="!opportunity || load_status == 'error'" id="opportunity-head-error-container">
            <p>{{ translations.head_error[lang] }}</p>
        </div>
        <template v-else>
            <div id="opportunity-title-container">
                <p>{{ opportunity.title }}</p>
                <div>
                    <a id="provider-container" :href="`/opportunities?provider=${opportunity.provider.id}`">
                        <img :src="opportunity.provider.logo">
                        <p>{{ opportunity.provider.name }}</p>
                    </a>
                    <p>/</p>
                    <a id="opportunity-category" :href="`/opportunities?category=${opportunity.category}`">
                        {{ capitalize(opportunity.category) }}
                    </a>
                </div>
            </div>
            <p>{{ opportunity.description }}</p>
            <a id="visit-opportunity-page-button" :href="`/opportunity/${opportunity.id}#sections`">
                {{ translations.visit_page_button[lang] }}
            </a>
        </template>
    </div>
    <div id="opportunity-side-container">
        <div id="opportunity-tags-container" class="container">
            <div class="title-container">
                <img class="img" src="~/public/hash.png">
                <p class="title">{{ translations.tags[lang] }}</p>
            </div>
            <div v-if="load_status == 'pending'" class="pending-container">
                <img src="~/public/loading.gif" class="loader">
            </div>
            <div v-else-if="!opportunity || load_status == 'error'" class="error-container">
                <p>{{ translations.tags_error[lang] }}</p>
            </div>
            <div v-else class="tags-container">
                <p v-if="opportunity.tags.length === 0" id="tags-empty">
                    {{ translations.no_tags[lang] }}
                </p>
                <a v-for="{ id, name } in opportunity.tags.slice(0, 3)" class="tag" :href="`/opportunities?tag=${id}`">
                    <p>{{ name }}</p>
                </a>
                <p v-if="opportunity.tags.length > 3" id="opportunity-tag-wildcard" class="tag">...</p>
            </div>
        </div>
        <div id="opportunity-places-container" class="container">
            <div class="title-container">
                <img class="img" src="~/public/place.png">
                <p class="title">{{ translations.places[lang] }}</p>
            </div>
            <div v-if="load_status == 'pending'" class="pending-container">
                <img src="~/public/loading.gif" class="loader">
            </div>
            <div v-else-if="!opportunity || load_status == 'error'" class="error-container">
                <p>{{ translations.places_error[lang] }}</p>
            </div>
            <div v-else class="places-container">
                <p v-if="opportunity.places.length === 0" id="places-empty">
                    {{ translations.no_places[lang] }}
                </p>
                <a v-for="{ id, name } in opportunity.places.slice(0, 3)" class="tag"
                    :href="`/opportunities?place=${id}`">
                    <p>{{ name }}</p>
                </a>
                <p v-if="opportunity.places.length > 3" id="opportunity-place-wildcard" class="tag">...</p>
            </div>
        </div>
    </div>
</template>

<style>
#content-container {
    height: calc(100vh - 50px);
    background-color: #141414;
    display: grid;
    grid-template-columns: 5% 60% 1fr 25% 5%;
    grid-template-rows: 5% 1fr 5%;
}

#opportunity-head-container {
    grid-column: 2 / 3;
    grid-row: 2 / 3;
    font-size: 24px;
    display: flex;
    flex-direction: column;
    gap: 30px;
    justify-content: center;
}

#opportunity-head-pending-container {
    align-self: center;
}

#opportunity-head-pending-container .loader {
    height: 32px;
    aspect-ratio: 1 / 1;
}

#opportunity-head-error-container {
    align-self: center;
    color: white;
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

#opportunity-head-container>p {
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

#opportunity-side-container .pending-container,
#opportunity-side-container .error-container,
#opportunity-tags-container>.tags-container,
#opportunity-places-container>.places-container {
    grid-column: 2 / 3;
    grid-row: 4 / 5;
}

#opportunity-side-container .pending-container img {
    height: 1em;
    max-width: 1em;
    aspect-ratio: 1 / 1;
}

#opportunity-side-container .error-container p {
    font-size: .5em;
    color: white;
}

#opportunity-tags-container>.tags-container,
#opportunity-places-container>.places-container {
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

#opportunity-tag-wildcard,
#opportunity-place-wildcard {
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
