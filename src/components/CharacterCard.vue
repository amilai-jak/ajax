<script setup>
defineProps(['character']);
</script>

<template>
    <div class="card">
        <header class="card-header">
            <p class="card-header-title">{{ character.name }}</p>
        </header>
        <div class="card-image">
            <figure class="image is-4by3">
                <img :src="character.image" :alt="character.name" loading="lazy" />
            </figure>
        </div>
        <div class="card-content">
            <div class="content">
                <table>
                    <tbody>
                        <tr>
                            <th>Status</th>
                            <td><span class="tag" :class="{
                                'is-success': character.status === 'Alive',
                                'is-danger': character.status === 'Dead',
                                'is-light': character.status === 'unknown',
                            }">{{ character.status }}</span></td>
                        </tr>
                        <tr>
                            <th>Gender</th>
                            <td><span class="tag is-info is-light">{{ character.gender }}</span></td>
                        </tr>
                        <tr>
                            <th>Type</th>
                            <td>{{ character.type ? character.type : 'No type' }}</td>
                        </tr>
                        <tr>
                            <th>Species</th>
                            <td>{{ character.species }}</td>
                        </tr>
                        <tr>
                            <th>Location</th>
                            <td>{{ character.location.name }}</td>
                        </tr>
                        <tr>
                            <th>Origin</th>
                            <td>{{ character.origin.name }}</td>
                        </tr>
                        <tr>
                            <th>Episodes</th>
                            <td>{{ character.episode.length }}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</template>
