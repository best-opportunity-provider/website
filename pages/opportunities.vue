<script setup lang="ts">
import { useHead, useCookie, createError, navigateTo, ref, computed, onMounted } from '#imports'
import { FetchError } from 'ofetch';
import Header from '~/components/Header.vue';
import Footer from '~/components/Footer.vue';
import OpportunityCard from '~/components/OpportunityCard.vue';

const api_host = 'localhost:8001';
const lang = useCookie<'en' | 'ru'>('lang', {
    default: () => 'en',
    maxAge: 3600 * 24 * 3650,
});

if (lang.value !== 'en' && lang.value !== 'ru') {
    lang.value = 'en';
}

const api_key = useCookie<string | undefined>(
    'api_key', { default: () => undefined }
)

const opportunities = ref<string[]>([]);
const opportunities_request_status = ref<'pending' | 'success'>('pending');

const active_opportunity = ref<number | null>(null);
const active_id = computed(() => {
    if (active_opportunity.value === null) {
        return null;
    }
    return opportunities.value[active_opportunity.value];
});

type Industry = {
    id: string,
    name: string,
};

const industries = ref<Industry[]>([]);
const selected_industries = ref<string[]>([]);
const industries_request_status = ref<'idle' | 'pending' | 'success' | 'error'>('idle');

async function request_industries() {
    if (industries_request_status.value !== 'idle') {
        return;
    }
    industries_request_status.value = 'pending';
    const result = await $fetch<Industry[]>(
        `${lang.value}/opportunity-industry/all`,
        {
            method: 'GET',
            baseURL: `http://${api_host}`,
            query: {
                api_key: api_key.value,
            },
        }
    ).catch(async (error: FetchError) => {
        if (error.statusCode === 403) {
            api_key.value = undefined;
            await navigateTo('/sign-in');
        }
        industries_request_status.value = 'error';
        return null;
    });
    if (result === null) {
        return;
    }
    industries.value = result;
    industries_request_status.value = 'success';
}

type Tag = {
    id: string,
    name: string,
};

const tags = ref<Tag[]>([]);
const selected_tags = ref<string[]>([]);
const tags_request_status = ref<'idle' | 'pending' | 'success' | 'error'>('idle');

async function request_tags() {
    if (tags_request_status.value !== 'idle') {
        return;
    }
    tags_request_status.value = 'pending';
    const result = await $fetch<Tag[]>(
        `${lang.value}/opportunity-tag/all`,
        {
            method: 'GET',
            baseURL: `http://${api_host}`,
            query: {
                api_key: api_key.value,
            },
        }
    ).catch(async (error: FetchError) => {
        if (error.statusCode === 403) {
            api_key.value = undefined;
            await navigateTo('/sign-in');
        }
        tags_request_status.value = 'error';
        return null;
    });
    if (result === null) {
        return;
    }
    tags.value = result;
    tags_request_status.value = 'success';
}

type Provider = {
    id: string,
    name: string,
}

const providers = ref<Provider[]>([]);
const selected_providers = ref<string[]>([]);
const providers_request_status = ref<'idle' | 'pending' | 'success' | 'error'>('idle');

async function request_providers() {
    if (providers_request_status.value !== 'idle') {
        return;
    }
    providers_request_status.value = 'pending';
    const result = await $fetch<Provider[]>(
        `${lang.value}/opportunity-provider/all`,
        {
            method: 'GET',
            baseURL: `http://${api_host}`,
            query: {
                api_key: api_key.value,
            },
        }
    ).catch(async (error: FetchError) => {
        if (error.statusCode === 403) {
            api_key.value = undefined;
            await navigateTo('/sign-in');
        }
        providers_request_status.value = 'error';
        return null;
    });
    if (result === null) {
        return;
    }
    providers.value = result;
    providers_request_status.value = 'success';
}

async function fetch_opportunities() {
    opportunities_request_status.value = 'pending';
    opportunities.value = [];
    active_opportunity.value = null;
    const result = await $fetch<string[]>(
        `${lang.value}/opportunities`,
        {
            method: 'POST',
            baseURL: `http://${api_host}`,
            query: {
                api_key: api_key.value,
            },
            body: {
                providers: selected_providers.value,
                industries: selected_industries.value,
                tags: selected_tags.value,
            },
        }
    ).catch(async (error: FetchError) => {
        if (error.statusCode === 403) {
            api_key.value = undefined;
            await navigateTo('/sign-in');
        }
        throw createError({
            statusCode: error.statusCode,
            statusMessage: 'Something went wrong',
            fatal: true,
        });
    });
    opportunities.value = result;
    active_opportunity.value = result.length !== 0 ? 0 : null;
    opportunities_request_status.value = 'success';
}

onMounted(async () => {
    if (api_key.value === undefined) {
        await navigateTo('/sign-in');
    }
    await fetch_opportunities();
})

const translations = {
    page_title: {
        en: 'Opportunities',
        ru: 'Возможности',
    },
}

useHead({
    title: `${translations.page_title[lang.value]} - Offer`,
})
</script>

<template>
    <Header :lang="lang" :api_host="'localhost:8001'" :active_nav_section="0" :must_fill_info="true" />
    <div id="content-container">
        <ClientOnly>
            <img v-if="opportunities_request_status === 'pending'" src="~/public/loading.gif">
            <p v-else-if="active_opportunity === null" style="color: white;">
                No opportunities found, <a href="/opportunities">reset filters</a>
            </p>
            <OpportunityCard v-else ref="opportunity-card" :api_host="api_host" :lang="lang"
                :opportunity_id="active_id">
                <button @click="active_opportunity -= 1" :disabled="active_opportunity === 0">Previous</button>
                <button @click="active_opportunity += 1"
                    :disabled="active_opportunity === opportunities.length - 1">Next</button>
                <div style="max-height: 200px; overflow-y: scroll;">
                    <details style="color: white;" @toggle="request_industries">
                        <summary>Industry</summary>
                        <img v-if="industries_request_status === 'pending'" src="~/public/loading.gif"
                            style="height: 2em;">
                        <template v-else-if="industries_request_status === 'error'">
                            <p>Error happened</p>
                            <button @click="industries_request_status = 'idle', request_industries()">
                                Retry
                            </button>
                        </template>
                        <div v-else
                            style="display: flex; flex-direction: column; max-height: 100px; overflow-y: scroll;">
                            <template v-for="{ id, name } in industries">
                                <div>
                                    <input type="checkbox" :id="`industry-${id}`" :value="id"
                                        v-model="selected_industries">
                                    <label :for="`industry-${id}`" style="font-size: 14px;">{{ name }}</label>
                                </div>
                            </template>
                        </div>
                    </details>
                    <details style="color: white;" @toggle="request_tags">
                        <summary>Tags</summary>
                        <img v-if="tags_request_status === 'pending'" src="~/public/loading.gif" style="height: 2em;">
                        <template v-else-if="tags_request_status === 'error'">
                            <p>Error happened</p>
                            <button @click="tags_request_status = 'idle', request_tags()">
                                Retry
                            </button>
                        </template>
                        <div v-else
                            style="display: flex; flex-direction: column; max-height: 100px; overflow-y: scroll;">
                            <template v-for="{ id, name } in tags">
                                <div>
                                    <input type="checkbox" :id="`tag-${id}`" :value="id" v-model="selected_tags">
                                    <label :for="`tag-${id}`" style="font-size: 14px;">{{ name }}</label>
                                </div>
                            </template>
                        </div>
                    </details>
                    <details style="color: white;" @toggle="request_providers">
                        <summary>Provider</summary>
                        <img v-if="providers_request_status === 'pending'" src="~/public/loading.gif"
                            style="height: 2em;">
                        <template v-else-if="providers_request_status === 'error'">
                            <p>Error happened</p>
                            <button @click="providers_request_status = 'idle', request_providers()">
                                Retry
                            </button>
                        </template>
                        <div v-else
                            style="display: flex; flex-direction: column; max-height: 100px; overflow-y: scroll;">
                            <template v-for="{ id, name } in providers">
                                <div>
                                    <input type="checkbox" :id="`provider-${id}`" :value="id"
                                        v-model="selected_providers">
                                    <label :for="`provider-${id}`" style="font-size: 14px;">{{ name }}</label>
                                </div>
                            </template>
                        </div>
                    </details>
                    <button @click="fetch_opportunities">Apply</button>
                </div>
            </OpportunityCard>
        </ClientOnly>
    </div>
    <Footer :lang="lang" />
</template>
