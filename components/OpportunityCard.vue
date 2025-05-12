<script setup lang="ts">
import { createError, navigateTo, useCookie, useFetch, useLazyFetch, type AsyncDataRequestStatus } from '#app';
import { onMounted, onUpdated, ref, watch, type Ref } from 'vue';
import { FetchError } from 'ofetch';

const props = defineProps<{
    lang: 'en' | 'ru',
    api_host: string,
    opportunity_id: string,
}>();

type NamedObject = {
    id: string,
    name: string,
};

type Category = 0 | 1;

type Opportunity = {
    id: string,
    name: string,
    description: string,
    provider: NamedObject & {
        logo: string,  // URL
    },
    category: Category,
    tags: NamedObject[],
    places: NamedObject[],
};

function categoryName(category: Number) {
    switch (category) {
        case 0:
            return 'Internship'
        case 1:
            return 'Job'
    }
}

const api_key = useCookie<string | undefined>(
    'api_key', { default: () => undefined }
)

let opportunity = ref<Opportunity | null>(null);
let load_status = ref<AsyncDataRequestStatus>('idle');
let load_error = ref<FetchError | null>(null);
const for_paid = ref(false);

async function fetch_opportunity() {
    load_status.value = 'pending';
    opportunity.value = await $fetch<Opportunity>(
        `${props.lang}/opportunity`,
        {
            method: 'GET',
            baseURL: `http://${props.api_host}`,
            query: {
                opportunity_id: props.opportunity_id,
                api_key: api_key.value,
            },
        }
    ).catch((error: FetchError) => {
        load_status.value = 'error';
        load_error.value = error;
        return null;
    });
    if (opportunity.value !== null) {
        load_status.value = 'success';
    }
}

onMounted(async () => {
    if (api_key.value === undefined) {
        await navigateTo('/sign-in');
    }
    fetch_opportunity();
});

watch(() => props.opportunity_id, () => {
    fetch_opportunity();
    submit_status.value = 'waiting';
});

watch(load_error, async () => {
    if (load_error.value === null) {
        return;
    }
    if (load_error.value.statusCode === 403) {
        api_key.value = undefined
        await navigateTo('/sign-in');
    }
    console.log(load_error.value.data);
    // only for paid users
    if (load_error.value.statusCode === 422 && load_error.value.data.query.opportunity_id[0].type === 204) {
        for_paid.value = true;
        return;
    }
    throw createError({
        statusCode: load_error.value.statusCode,
        statusMessage: 'Something went wrong',
        fatal: true,
    });
});

const submit_status = ref<'error' | 'success' | 'pending' | 'waiting'>('waiting');

async function submit() {
    submit_status.value = 'pending';
    let { error } = await useFetch(
        `${props.lang}/opportunity/response`,
        {
            method: 'POST',
            baseURL: `http://${props.api_host}`,
            query: {
                api_key: api_key,
                opportunity_id: props.opportunity_id,
            },
            cache: 'no-cache',
        }
    );
    if (error.value === null) {
        submit_status.value = 'success';
        return;
    }
    if (error.value.statusCode === 403) {
        api_key.value = undefined;
        await navigateTo('/sign-in');
    }
    submit_status.value = 'error';
}

const translations = {
    apply_vacancy: {
        en: 'Apply for a vacancy',
        ru: 'Податься на вакансию',
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
    head_paid_error: {
        en: 'This opportunity is available only for subscribers',
        ru: 'Эта возможность доступна только с оформленной подпиской',
    },
    tags_error: {
        en: 'Couldn\'t load tags',
        ru: 'Ошибка загрузки тегов',
    },
    places_error: {
        en: 'Couldn\'t load places',
        ru: 'Ошибка загрузки мест',
    },
    apply_error: {
        en: 'Error happened',
        ru: 'Произошла ошибка',
    },
    apply_success: {
        en: 'You successfully applied!',
        ru: 'Вы успешно подались на вакансию!',
    },
}
</script>

<template>
    <div id="opportunity-head-container" @opportunity-update="fetch_opportunity()">
        <div v-if="load_status === 'pending'" id="opportunity-head-pending-container">
            <img src="~/public/loading.gif" class="loader">
        </div>
        <div v-else-if="!opportunity || load_status == 'error'" id="opportunity-head-error-container">
            <p v-if="!for_paid">{{ translations.head_error[lang] }}</p>
            <p v-else>{{ translations.head_paid_error[lang] }}</p>
        </div>
        <template v-else>
            <div id="opportunity-title-container">
                <p>{{ opportunity.name }}</p>
                <div>
                    <a id="provider-container" :href="`/opportunities?provider=${opportunity.provider.id}`">
                        <img
                            :src="`http://${props.api_host}/${props.lang}/file?id=${opportunity.provider.logo}&api_key=${api_key}`">
                        <p>{{ opportunity.provider.name }}</p>
                    </a>
                    <p>/</p>
                    <a id="opportunity-category" :href="`/opportunities?category=${opportunity.category}`">
                        {{ categoryName(opportunity.category) }}
                    </a>
                </div>
            </div>
            <p>{{ opportunity.description }}</p>
            <button id="visit-opportunity-page-button" @click="submit" :disabled="submit_status !== 'waiting'">
                <p v-if="submit_status === 'waiting'">
                    {{ translations.apply_vacancy[lang] }}
                </p>
                <p v-else-if="submit_status === 'error'">
                    {{ translations.apply_error[lang] }}
                </p>
                <p v-else-if="submit_status === 'success'">
                    {{ translations.apply_success[lang] }}
                </p>
                <img v-else style="height: 2em;" src="~/public/loading.gif" />
            </button>
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
                <a v-for="{ id, name } in opportunity.tags.slice(0, 3)" class="tag">
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
                <a v-for="{ id, name } in opportunity.places.slice(0, 3)" class="tag">
                    <p>{{ name }}</p>
                </a>
                <p v-if="opportunity.places.length > 3" id="opportunity-place-wildcard" class="tag">...</p>
            </div>
        </div>
        <slot></slot>
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
    display: -webkit-box;
    -webkit-box-orient: vertical;
    line-clamp: 5;
    -webkit-line-clamp: 5;
    color: white;
    font-weight: 250;
    overflow: hidden;
    text-overflow: ellipsis;
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
