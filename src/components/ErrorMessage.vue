<script setup>
const props = defineProps({
    message: {
        type: String,
        required: true,
    },
});
</script>

<template>
    <p class="help is-danger" data-cy="ErrorMessage">{{ props.message }}</p>
</template>
