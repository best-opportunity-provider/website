<script setup>
import Footer from '~/components/Footer.vue';

const title = 'Intern in the team of industrial design of Smart devices with Alice';
const provider = 'Yandex';
const category = 'Internship';
const short_description = 'We are looking for talented interns who want to develop their skills in industrial design, work with real devices and participate in creating the future.';
const tags = ['Hybrid', 'Industrial design', 'User interface', 'ZXC', 'QWE']
</script>

<template>
    <div id="content-container">
        <div id="opportunity-header">
            <div id="opportunity-header-general">
                <h2>{{ title }}</h2>
                <div id="opportunity-header-subheading">
                    <a id="opportunity-category-tag" :href="`/opportunities?category=${category}`">{{ category }}</a>
                    <p>/</p>
                    <a id="opportunity-provider-tag" :href="`/opportunities?provider=${provider}`">
                        {{ provider }}
                    </a>
                </div>
                <p>{{ short_description }}</p>
                <div id="opportunity-header-tags">
                    <a v-for="tag in tags.slice(0, 3)" class="opportunity-tag" :href="`/opportunities?tag=${tag}`">
                        <p>{{ tag }}</p>
                    </a>
                    <p v-if="tags.length > 3" id="opportunity-tags-wildcard" class="opportunity-tag">...</p>
                </div>
            </div>
        </div>
        <div id="opportunity-body">
            <div id="opportunity-details"></div>
            <div id="opportunity-sections-container">
                <!-- ... -->
            </div>
        </div>
    </div>
    <Footer />
</template>

<style>
#content-container {
    display: block;
    width: 100%;
}

@media (min-width: 700px) {
    #content-container {
        font-size: 24px;
    }
}

@media (min-width: 400px) and (max-width: 700px) {
    #content-container {
        font-size: 18px;
    }
}

#opportunity-header {
    width: 100%;
    height: 100vh;
    display: grid;
    grid-template-columns: min(5%, 30px) auto min(5%, 30px);
    grid-template-rows: minmax(auto, 30px) auto minmax(auto, 30px);
    background: linear-gradient(to right top, #e6fcff, white);
}

#opportunity-header-general {
    width: fit-content;
    grid-column: 2 / 3;
    grid-row: 2 / 3;
}

#opportunity-header-general>h2 {
    display: -webkit-box;
    max-width: min(80vw, 80%);
    font-size: 200%;
    font-weight: 400;
    -webkit-box-orient: vertical;
    line-clamp: 3;
    -webkit-line-clamp: 3;
    overflow: hidden;
    text-overflow: ellipsis;
}

#opportunity-header-subheading {
    margin-top: 10px;
    display: flex;
    flex-direction: row;
    justify-content: left;
    align-items: center;
    gap: 8px;
    font-size: 75%;
}

#opportunity-provider-tag,
#opportunity-category-tag {
    color: dodgerblue;
    font-size: 110%;
    text-decoration: none;
}

#opportunity-provider-tag:hover,
#opportunity-category-tag:hover {
    text-decoration: underline;
}

#opportunity-header-subheading>p {
    color: #b0b0b0;
    user-select: none;
}

#opportunity-provider-container {
    gap: 3px;
}

#opportunity-provider-container>img {
    max-width: 1.2em;
    max-height: 1.2em;
}

#opportunity-header-general>p {
    max-width: min(70vw, 70%);
    margin-top: 40px;
}

#opportunity-header-tags {
    margin-top: 10px;
    display: flex;
    flex-direction: row;
    justify-content: left;
    align-items: center;
    gap: 8px;
    font-size: 75%;
}

#opportunity-tags-wildcard {
    aspect-ratio: 1 / 1;
    cursor: pointer;
    user-select: none;
    color: #b0b0b0;
    transition: color 0.3s, border-color 0.3s;
}

#opportunity-tags-wildcard:hover {
    color: dodgerblue;
    transition: color 0.3s, border-color 0.3s;
}

.opportunity-tag {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    width: fit-content;
    height: 2em;
    background-color: white;
    border-radius: 5px;
    border: 1.5px solid #d3d3d3;
    transition: border-color 0.3s;
    padding: 4px 6px 4px 6px;
    color: #575757;
    font-weight: 400;
    text-decoration: none;
}

.opportunity-tag:hover {
    border: 1.5px solid dodgerblue;
    transition: border-color 0.3s;
}
</style>
