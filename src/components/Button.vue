<script setup>
import { computed } from 'vue';

const props = defineProps({
    color: {
        type: String,
        validator: value => ['white', 'light', 'dark', 'black', 'text', 'ghost', 'primary', 'link', 'info', 'success', 'warning', 'danger'].includes(value),
    },
    size: {
        type: String,
        validator: value => ['small', 'normal', 'medium', 'large'].includes(value),
        default: 'normal',
    },
    type: {
        type: String,
        validator: value => ['button', 'submit', 'reset'].includes(value),
        default: 'button',
    },
    light: Boolean,
    outlined: Boolean,
    rounded: Boolean,
    fullwidth: Boolean,
    loading: Boolean,
    disabled: Boolean,
});

const classes = computed(() => [
    props.color && `is-${props.color}`,
    `is-${props.size}`,
    {
        'is-light': props.light,
        'is-outlined': props.outlined,
        'is-rounded': props.rounded,
        'is-fullwidth': props.fullwidth,
        'is-loading': props.loading,
    },
]);
</script>

<template>
    <button class="button" :class="classes" :type="type"
        :disabled="disabled || loading" :aria-busy="loading">
        <slot></slot>
    </button>
</template>
