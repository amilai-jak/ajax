<script setup>
import axios from 'axios';
import { computed, onMounted, onUnmounted, reactive, ref } from 'vue';
import Button from '../components/Button.vue';
import CharacterCard from '../components/CharacterCard.vue';

const characters = ref([]);
const emptyPagination = () => ({ count: 0, pages: 0, next: null, prev: null });
const pagination = ref(emptyPagination());
const current = ref(1);
const searchInput = ref('');
const filters = reactive({ status: '', gender: '' });
const loading = ref(false);
const error = ref('');
const statuses = ['alive', 'dead', 'unknown'];
const genders = ['female', 'male', 'genderless', 'unknown'];
const hasFilters = computed(() => Boolean(searchInput.value || filters.status || filters.gender));
let searchTimeout;
let controller;

async function getCharacters(page = 1) {
    clearTimeout(searchTimeout);
    controller?.abort();
    const requestController = new AbortController();
    controller = requestController;
    loading.value = true;
    error.value = '';
    current.value = page;

    const params = new URLSearchParams();
    if (page > 1) params.set('page', String(page));
    if (searchInput.value.trim()) params.set('name', searchInput.value.trim());
    if (filters.status) params.set('status', filters.status);
    if (filters.gender) params.set('gender', filters.gender);

    try {
        const res = await axios.get('https://rickandmortyapi.com/api/character/', {
            params,
            signal: requestController.signal,
        });
        if (requestController.signal.aborted) return;
        characters.value = res.data.results;
        pagination.value = res.data.info;
    } catch (err) {
        if (requestController.signal.aborted) return;
        characters.value = [];
        pagination.value = emptyPagination();
        // The API uses 404 when no characters match the selected filters.
        if (err.response?.status !== 404) {
            error.value = 'Could not load characters. Please try again.';
        }
    } finally {
        if (controller === requestController) loading.value = false;
    }
}

function selectFilter(category, value) {
    filters[category] = value;
    getCharacters(1);
}

function clearFilters() {
    searchInput.value = '';
    filters.status = '';
    filters.gender = '';
    getCharacters(1);
}

function search() {
    clearTimeout(searchTimeout);
    searchTimeout = setTimeout(() => getCharacters(1), 500);
}

const pages = computed(() => {
    const total = pagination.value.pages;
    const visible = new Set([1, 2, 3, total - 2, total - 1, total]);
    for (let page = current.value - 2; page <= current.value + 2; page++) visible.add(page);
    const sorted = [...visible].filter(page => page >= 1 && page <= total).sort((a, b) => a - b);
    const result = [];
    for (const page of sorted) {
        if (result.length && page - result[result.length - 1] > 1) result.push('...');
        result.push(page);
    }
    return result;
});

onMounted(() => getCharacters());
onUnmounted(() => {
    clearTimeout(searchTimeout);
    controller?.abort();
});
</script>

<template>
    <main class="section">
        <div class="container" :aria-busy="loading">
            <h1 class="title">Rick and Morty</h1>
            <form class="field has-addons" role="search" @submit.prevent="getCharacters(1)">
                <div class="control is-expanded">
                    <label class="is-sr-only" for="character-search">Find a character</label>
                    <input id="character-search" v-model="searchInput" @input="search" class="input"
                        type="search" placeholder="Find a character">
                </div>
                <div class="control">
                    <Button type="submit" color="info" :loading="loading">Search</Button>
                </div>
            </form>

            <div class="field is-grouped is-grouped-multiline">
                <div class="control">
                    <p id="status-label" class="label">Status</p>
                    <div class="buttons has-addons" role="group" aria-labelledby="status-label">
                        <Button size="small" :color="filters.status === '' ? 'link' : 'light'"
                            :aria-pressed="filters.status === ''" :disabled="loading" @click="selectFilter('status', '')">All</Button>
                        <Button v-for="status in statuses" :key="status" size="small"
                            :color="filters.status === status ? 'link' : 'light'"
                            :aria-pressed="filters.status === status" :disabled="loading"
                            @click="selectFilter('status', status)">{{ status }}</Button>
                    </div>
                </div>
                <div class="control">
                    <p id="gender-label" class="label">Gender</p>
                    <div class="buttons has-addons" role="group" aria-labelledby="gender-label">
                        <Button size="small" :color="filters.gender === '' ? 'primary' : 'light'"
                            :aria-pressed="filters.gender === ''" :disabled="loading" @click="selectFilter('gender', '')">All</Button>
                        <Button v-for="gender in genders" :key="gender" size="small"
                            :color="filters.gender === gender ? 'primary' : 'light'"
                            :aria-pressed="filters.gender === gender" :disabled="loading"
                            @click="selectFilter('gender', gender)">{{ gender }}</Button>
                    </div>
                </div>
                <div class="control is-align-self-flex-end">
                    <div class="buttons">
                        <Button size="small" color="danger" outlined :disabled="loading || !hasFilters" @click="clearFilters">Clear All</Button>
                    </div>
                </div>
            </div>

            <p v-if="loading" role="status" class="notification is-info is-light">Loading characters...</p>
            <div v-else-if="error" role="alert" class="notification is-danger is-light">
                <p class="mb-3">{{ error }}</p>
                <Button color="danger" outlined @click="getCharacters(current)">Try again</Button>
            </div>
            <p v-else-if="!characters.length" role="status" class="notification is-warning is-light">
                No characters match your search and filters.
            </p>
            <p v-else role="status" class="mb-4">{{ pagination.count }} characters found</p>

            <nav v-if="pagination.pages > 1" class="pagination is-centered" aria-label="Character pages">
                <button class="pagination-previous" :disabled="loading || !pagination.prev" @click="getCharacters(current - 1)">Previous</button>
                <button class="pagination-next" :disabled="loading || !pagination.next" @click="getCharacters(current + 1)">Next page</button>
                <ul class="pagination-list">
                    <li v-for="(page, index) in pages" :key="`${page}-${index}`">
                        <span v-if="page === '...'" class="pagination-ellipsis">&hellip;</span>
                        <button v-else class="pagination-link" :class="{ 'is-current': page === current }"
                            :aria-label="`Go to page ${page}`" :aria-current="page === current ? 'page' : undefined"
                            :disabled="loading || page === current" @click="getCharacters(page)">{{ page }}</button>
                    </li>
                </ul>
            </nav>
            <div v-if="!loading && !error" class="columns is-multiline">
                <div v-for="character in characters" :key="character.id" class="column is-12-mobile is-6-tablet is-3-desktop">
                    <CharacterCard :character="character" />
                </div>
            </div>
        </div>
    </main>
</template>
