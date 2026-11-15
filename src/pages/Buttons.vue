<script setup>
import { ref } from 'vue';
import Button from '../components/Button.vue';

const colors = ['primary', 'link', 'info', 'success', 'warning', 'danger', 'white', 'light', 'dark', 'black', 'text', 'ghost'];
const sizes = ['small', 'normal', 'medium', 'large'];
const selectedColor = ref('primary');
const clicks = ref(0);
const submitted = ref(false);
</script>

<template>
    <main class="section">
        <div class="container">
            <h1 class="title">Buttons</h1>
            <p class="subtitle">Reusable Bulma buttons with validated props and slot content.</p>

            <section class="box">
                <h2 class="title is-4">Colors</h2>
                <div class="buttons">
                    <Button>Default</Button>
                    <Button v-for="color in colors" :key="color" :color="color">{{ color }}</Button>
                </div>
                <pre><code>&lt;Button color="primary"&gt;Primary&lt;/Button&gt;</code></pre>
            </section>

            <section class="box">
                <h2 class="title is-4">Sizes</h2>
                <div class="buttons">
                    <Button v-for="size in sizes" :key="size" color="link" :size="size">{{ size }}</Button>
                </div>
                <pre><code>&lt;Button color="link" size="small"&gt;Small&lt;/Button&gt;</code></pre>
            </section>

            <section class="box">
                <h2 class="title is-4">Styles and states</h2>
                <div class="buttons">
                    <Button color="primary" light>Light</Button>
                    <Button color="danger" outlined>Outlined</Button>
                    <Button color="info" rounded>Rounded</Button>
                    <Button color="link" loading>Loading</Button>
                    <Button color="success" disabled>Disabled</Button>
                    <Button color="primary" outlined rounded>Combined props</Button>
                </div>
                <Button color="primary" fullwidth>Full width</Button>
                <pre class="mt-4"><code>&lt;Button color="danger" outlined&gt;Outlined&lt;/Button&gt;
&lt;Button color="link" loading&gt;Loading&lt;/Button&gt;</code></pre>
            </section>

            <section class="box">
                <h2 class="title is-4">Reactive props, events and slots</h2>
                <div class="field">
                    <label class="label" for="button-color">Button color</label>
                    <div class="control">
                        <div class="select">
                            <select id="button-color" v-model="selectedColor">
                                <option v-for="color in colors" :key="color" :value="color">{{ color }}</option>
                            </select>
                        </div>
                    </div>
                </div>
                <Button :color="selectedColor" @click="clicks++">Clicked <strong class="ml-1">{{ clicks }}</strong></Button>
                <p class="help" aria-live="polite">Click the button or change its color above.</p>
                <pre class="mt-4"><code>&lt;Button :color="selectedColor" @click="clicks++"&gt;Clicked {{ clicks }}&lt;/Button&gt;</code></pre>
            </section>

            <section class="box">
                <h2 class="title is-4">Form submission</h2>
                <form @submit.prevent="submitted = true" @reset="submitted = false">
                    <div class="buttons">
                        <Button type="submit" color="success">Submit</Button>
                        <Button type="reset" outlined>Reset</Button>
                    </div>
                    <p aria-live="polite">{{ submitted ? 'Form submitted.' : 'Ready to submit.' }}</p>
                </form>
                <pre class="mt-4"><code>&lt;Button type="submit" color="success"&gt;Submit&lt;/Button&gt;</code></pre>
                <p class="help">The default type is button. Invalid color, size or type values produce a Vue warning in development.</p>
            </section>
        </div>
    </main>
</template>
